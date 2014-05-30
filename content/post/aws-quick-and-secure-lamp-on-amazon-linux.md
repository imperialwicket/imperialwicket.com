{
  "title": "AWS: Quick and secure LAMP on Amazon Linux",
  "description": "AWS: Quick and secure LAMP on Amazon Linux",
  "date": "2011-12-12",
  "url": "aws-quick-and-secure-lamp-on-amazon-linux",
  "type": "post",
  "tags": [
    "aws"
  ]
}
This is pretty cut and dry. If you want the more verbose version, head over to [AWS: Building a LAMP instance on Amazon Linux](http://imperialwicket.com/aws-building-a-lamp-instance).

Start an Amazon Linux instance (I default to 64 bit), and be sure the security group allows access over ports 22 and 80.

<pre>
sudo su -
yum update
# add the AMP
yum install httpd mysql mysql-server php php-mysql php-xml php-pdo php-odbc \
  php-soap php-common php-cli php-mbstring php-bcmath php-ldap php-imap php-gd

# Add a user and give sudo privs
useradd someUser
passwd someUser
# Give password
vim /etc/sudoers
# if this is unfamiliar to you, be careful:
# insert line "someUser ALL=(ALL)   ALL"

# Configure ssh key and disable password authentication
cd /home/someUser
mkdir .ssh
vim .ssh/authorized_keys
chown -R someUser:someUser .ssh/
chmod 700 .ssh
chmod 600 .ssh/*
vim /etc/ssh/ssh_config
# insert line "PasswordAuthentication no"
service sshd restart
# Validate connection in another terminal before exiting the current session!

# Primary MySQL config
chkconfig mysqld on
service mysqld start
/usr/bin/mysql_secure_installation
# Root access from local only
# Set a root password (that's good)
# Delete test db
# Delete anonymous users

# Apache chkconfig on
chkconfig httpd on
service httpd start

# Create a mysql user/schema for your site(s)
# DON'T CONNECT AS ROOT FOR YOUR WEB APPS
mysql -u root -p
# Enter the password you set
mysql> CREATE SCHEMA someAppName;
mysql> GRANT ALL ON someAppName.* TO someAppName@'%' IDENTIFIED BY 'somePassword';
</pre>

Now you can connect with ssh, using your key and the username you configured. Use MySQL Workbench's Standard TCP/IP over SSH connection type to tunnel to MySQL over SSH.

You will likely also want to create a key on this server and install a version control system so that you can pull code directly from your version control (you are using version control, right?!?).

I use mostly git these days, so:

<pre>
ssh-keygen -t rsa
sudo yum install git
</pre>

Copy the public key to your version control service, and you're good to go.
