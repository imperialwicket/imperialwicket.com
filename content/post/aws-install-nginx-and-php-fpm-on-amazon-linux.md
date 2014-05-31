{
  "title": "AWS: Install Nginx and PHP-FPM on Amazon Linux",
  "description": "AWS: Install Nginx and PHP-FPM on Amazon Linux",
  "date": "2012-05-10",
  "url": "aws-install-nginx-and-php-fpm-on-amazon-linux/",
  "type": "post",
  "tags": [
    "aws",
    "linux",
    "nginx"
  ]
}
First launch an Amazon Linux EC2 instance, and I'll add the usual caveat that much of this technique works on all Red Hat derivative distributions, though the package names and versions may be unique.

I am going to do this as the root user, and first start with a system update from the default repositories (always a good practice). 

<pre>
sudo su -
yum update
</pre>

### Installing Packages

After security updates and standard patches are applied to the server, install Nginx, PHP-FPM, and the PHP packages that many frameworks and CMS use.

<pre>
yum install nginx php-fpm php-xml php-pdo php-odbc \
  php-soap php-common php-cli php-mbstring php-bcmath php-ldap \
  php-imap php-gd php-pecl-apc 
</pre>

You likely require only one of the following commands, depending on your preference between MySQL and PostgreSQL. Amazon Linux (at time of writing) is offering MySQL 5.5 and PostgreSQL 9.1\. I will reference MySQL as the database server in the remainder of this article.

<pre>
yum install mysql-server mysql php-mysql 
yum install postgresql-server postgresql php-pgsql
</pre>

### Chkconfig and primary configuration

Use chkconfig so that when your server reboots your web server, php, and mysql start automatically.

<pre>
chkconfig nginx on
chkconfig mysqld on
chkconfig php-fpm on
</pre>

After starting the MySQL server, run the "mysql_secure_installation" script. Set the root password, remove anonymous users, limit root to local access, remove test db and access, update privs. 

<pre>
service mysqld start
mysql_secure_installation
</pre>

### Nginx and PHP-FPM configuration for EC2 Micro instance

I set this up predominantly on micro instances, so my config is going to be conservative in terms of memory usage. I will aim to keep the web server around 500MB, which does not leave a lot of memory for the kernel and mysql, but I have reasonable success with this configuration.

We want to throttle PHP-FPM a bit, add some failure handling mechanisms, and configure it to use a socket connection. 

<pre>
vim /etc/php-fpm.conf
</pre>

A lot of this is directly inspired by articles from [if-not-true-then-false](http://www.if-not-true-then-false.com/2011/nginx-and-php-fpm-configuration-and-optimizing-tips-and-tricks/). Update the following values in the php-fpm.conf file:

<pre>
emergency_restart_threshold 10
emergency_restart_interval 1m
process_control_timeout 15s
</pre>

Amazon Linux ships fpm with a default 'www.conf' php-fpm pool configuration file in the /etc/php-fpm.d/ directory. If you are planning to configure VirtualHost like behavior, it probably makes sense to distribute your different VirtualHosts to unique pools (vhost1.conf, vhost2.conf, etc. - conventionally). This allows you to configure PHP-FPM options particular to each VirtualHost.

We are going to use the default pool for this configuration, so open the www.conf file in your favorite text editor: 

<pre>vim /etc/php-fpm.d/www.conf</pre>

Now we must make the following changes to accommodate socket usage and allow socket access to the nginx user. We also must change the default Unix user/group for the PHP-FPM processes, which defaults to apache on the current Amazon Linux PHP-FPM package:

<pre>
;listen = 127.0.0.1:9000
listen = /var/run/php-fpm/php-fpm.sock
;listen.owner = nobody
listen.owner = nginx
;listen.group = nobody
listen.group = nginx
;listen.mode = 0666
listen.mode = 0664

user = nginx
group = nginx
</pre>

Our final updates to the PHP-FPM pool configuration limit memory that we want PHP-FPM to use. This is achieved by limiting the memory per process (php_admin_value[memory_limit]) and by limiting the number of processes PHP-FPM may spawn.

Still in the /etc/php-fpm.d/www.conf file:

<pre>
pm.max_children = 8
pm.start_servers = 3
pm.min_spare_servers = 3
pm.max_spare_servers = 6
pm.max_requests = 200

php_admin_value[memory_limit] = 64M
</pre>

Now for the Nginx configuration. We need to be sure that Nginx can talk to PHP-FPM, and that it knows how to pass php scripts to the server.

The core configuration for Nginx is available in the /etc/nginx/nginx.conf file, and contains sensible defaults in the Amazon Linux repositories. The Nginx VirtualHost configuration files are available in the /etc/nginx/conf.d/ directory, where a default, ssl, and virtual configuration are available with the Nginx installation. 

A proper configuration would setup default.conf to perform the appropriate actions, and likely create a unique file (i.e., vhost1.conf) to control each domain. For simplicity, we will edit the existing default.conf file in this configuration.

<pre>vim /etc/nginx/conf.d/default.conf</pre>

Most of the default configuration is perfectly appropriate, and we simply need to configure the socket connection, and ensure proper handling of *.php files. Add the 'index.php' reference the the root location block, so that the php file is loaded by default. Also add the block for directing *.php files to php-fpm.  

Note that this *.php redirect is not suitable for production use, as it directs any url ending in ".php" to PHP-FPM (that is, this allows a user to upload a file like "myRootKit.jpg", then execute its php code by loading: http://yourDomain.com/uploads/myRootKit.jpg.php). This is not a major concern for development usage or testing, but use caution and investigate more complex regex if you are planning to deploy this for production usage.

<pre>
location / {
    root   /usr/share/nginx/html;
    index  index.php index.html index.htm;
}

location ~ \.php$ {
      fastcgi_pass   unix:/var/run/php-fpm/php-fpm.sock;
      fastcgi_index  index.php;
      fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/html$fastcgi_script_name;
      include        fastcgi_params;
  }
</pre>

We are using the default Nginx document root in the /usr/share/nginx/html/ directory. In most cases you will want to configure your locations to use more unique root directories, but this corresponds to configuring multiple VirtualHost type configs, which we avoided.

With our configuration in place:

<pre>
service php-fpm start
service nginx start
</pre>

Create a test index.php file in /usr/share/nginx/html/ and then test the config by loading the root level of your domain/ip/public dns in your web browser.

### Notes

This is a basic implementation that should get you up and running with Nginx and PHP-FPM on Amazon Linux. There is a lot of information about virtual host configurations, security best practices, and Apache to Nginx transition that are worth further investigation.
