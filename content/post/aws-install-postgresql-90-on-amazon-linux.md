{
  "title": "AWS: Install PostgreSQL 9.0 on Amazon Linux",
  "description": "AWS: Install PostgreSQL 9.0 on Amazon Linux",
  "date": "2011-06-03",
  "url": "aws-install-postgresql-90-on-amazon-linux",
  "type": "post",
  "tags": [
    "aws",
    "postgresql",
    "linux"
  ]
}
[UPDATE: Since writing this, Amazon has updated their repos (several times) for Amazon Linux. You can still use pgrpms and get the latest and greatest PostgreSQL, and that's a fine technique. However, I want to point out that you can use: "sudo yum install postgresql-9.1 postgresql-server-9.1" to install PostgreSQL 9.1 directly from the Amazon Linux repos.]

This post is a follow up to the [Quick and dirty PostgreSQL installation on Amazon Linux](http://imperialwicket.com/aws-install-postgresql-on-amazon-linux-quick-and-dirty).  Use the previous article for quick installations; this post details an installation technique with which I would be more comfortable in a production environment.  This post also details installation from [pgrpms](http://pgrpms.org), instead of the Amazon repos.  The rpm installation is not as bleeding edge as building from source, but [pgrpms](http://pgrpms.org) stays quite current, and this avoids manual dealings with dependencies (which is nice).  

This production environment will have limited scalability, and the purpose of this post is not to detail each and every aspect of configuring the environment.  I am going to focus on getting the database server installed and running, and allowing reasonably secure access.  I am not going to cover backups; I am not going to go into details about PostgreSQL roles; I am not going to cover PostgreSQL tuning.  These are all things to consider, and after going through this installation walk-through, you will be in a reasonable position to consider them.  

The high level steps will be:

1.  Launch an EC2 instance
2.  Launch an EBS volume and mount it for our database cluster
3.  Add the pgrpms repository and disable PostgreSQL in the Amazon repositories
4.  Install and configure PostgreSQL access, ports, and listeners
5.  Start the PostgreSQL server
6.  Create Users for external access
7.  PgAdmin III usage

#### Launch an EC2 instance

This is a straight-forward EC2 instance running 64 bit Amazon Linux.  Points of note are that you probably should avoid Micro instances for a production database server, and be sure that you set an inbound rule for PostgreSQL in your security group.

1.  Login, navigate to EC2 tab.  Use the "Launch Instance" button to get started.
2.  Choose "Basic 64-bit Amazon Linux AMI"
3.  Generate a single instance, availability zone probably does not matter, and I am going to run with Micro (Micro is likely sufficient for proof of concept, but I would not recommend using it in a production server).
4.  Default "Advanced Instance Options" should be fine.
5.  Give your instance a meaningful value for the "Name" key.  This is a good habit.
6.  Create/Use a key pair for which you have the private key.
7.  Create a new security group - I like to dedicate security groups to server types.  If you already have a 'PostgreSQL' server, use that security group.  Avoid using the 'basic' security group for all of your servers and simply adding firewall rules whenever necessary.
8.  Security group config (Note the PostgreSQL port!!!)

    *   Group name: postgresql
    *   Group Description: PostgreSQL database server security group
    *   Inbound Rules: SSH - 22 from 0.0.0.0/0 and PostgreSQL 5434 from 0.0.0.0/0

9.  Launch the instance.
10.  Connect with something like:
ssh -i .ssh/myKey.pem ec2-user@ec2-11-11-11-111.compute-1.amazonaws.com

#### Launch an EBS volume and mount

We are going to create a dedicated EBS volume for our database cluster.  This provides a few benefits for our database server.  We can create periodic snapshots of the database without affecting availability of our EC2 instance.  We can then use those snapshots to launch new EBS volumes (allowing us to easily increase the size of the database volume, if necessary).  Alternatively, we can shut down the EC2 instance, create a new EC2, and point it at the original database volume.  

1.  In the AWS Management Console, select the Elastic Block Store "Volumes" option from the Navigation area of the EC2 tab.
2.  Select the "Create Volume" button
3.  Choose a size appropriate to your database needs
4.  Be sure the Availability Zone matches the zone where your root volume is located, and create the volume
5.  Select the new volume in your EBS Volumes list, and select the "Attach Volume" button.
6.  Choose your instance and assign a device ("/dev/sdb" will likely work)
7.  Connect to your EC2 instance, and execute ("/dev/sdb" must match the assigned device, and I am going to mount at "/pgdata")
<pre>
sudo su -
yes | mkfs -t ext3 /dev/sdb
mkdir /pgdata
mount /dev/sdb /pgdata
exit
</pre>

I am not certain why 'yes' is helpful/required here, I am going with [Amazon's recommendation](http://docs.amazonwebservices.com/AWSEC2/latest/UserGuide/).  I have not investigated, but if anyone can tell me how piping 'yes' to mkfs helps, I would love to know.

#### Update the yum repositories

I want to install the latest stable postgresql from [pgrpms.org](http://pgrpms.org).  We could just download the rpm and manually install from the file, but that inevitably results in some dependency issues.  I prefer to configure an alternate yum repository for a particular keyword.  So we need to update the configuration for the Amazon repositories (be sure to update both "main" and "updates", and do not forget the asterisk). 

<pre>
sudo vim /etc/yum.repos.d/amzn-main.repo
[At the bottom of the "[amzn-main]" section, after "enabled=1", add "exclude=postgresql*"]
sudo vim /etc/yum.repos.d/amzn-updates.repo
[Add the same exclude to the bottom of the "[amzn-updates]" section]
</pre>

Download the repository/key installation rpm from pgrpms.org
<pre>
wget http://yum.pgrpms.org/reporpms/9.0/pgdg-redhat-9.0-2.noarch.rpm
sudo rpm -ivh pgdg-redhat-9.0-2.noarch.rpm
</pre>

Since this rpm is generated for RHEL6, we need to make a minor change to the resulting /etc/yum.repos.d/pgdg-90-redhat.repo file.  Update the URLs, replacing the $releaseserver value with '6'.  The Amazon Linux $releaseserver value is a date, instead of the primary version number that this repository configuration is expecting.  
<pre>
sudo vim /etc/yum.repos.d/pgdg-90-redhat.repo
</pre>

The updated base url values should look like this (update two of them):

<pre>
## ORIGINAL
# baseurl=http://yum.pgrpms.org/9.0/redhat/rhel-$releasever-$basearch
## UPDATED
baseurl=http://yum.pgrpms.org/9.0/redhat/rhel-6-$basearch
</pre>

All we have done is told yum that it should use the amzn repositories for everything except packages that meet the "postgresql*" criteria.  After that, install a new repository configuration for the pgrpms.org repository.

#### Install and configure PostgreSQL 9.0 on Amazon Linux

After updating the yum repository configurations, "yum install postgresql*" should provide us with the latest postgresql* packages from pgrmps.org.  Notice that a few dependencies will come from the amazon repositories, but most of the pertinent postgresql* packages are coming from pgrpms.  It is extremely likely that you do not need all of these packages.  Limit the installation however you feel is appropriate.  

I usually install something like the following:
<pre>
sudo yum install postgresql90 postgresql90-contrib postgresql90-devel postgresql90-server 
</pre>

Now we need to initialize the database cluster, edit the configuration and start the server.  First remove the /pgdata/lost+found directory.  PostgreSQL's initdb will fail to initialize a database cluster in /pgdata/ when there are files/directories present.  Then we will change ownership of the /pgdata directory to the postgres user and group, and change to the postgres user.  As the postgres user, we can configure and launch the server.

<pre>
## Be careful with this
sudo rm -rf /pgdata/lost+found
sudo chown -R postgres:postgres /pgdata
sudo su -
su postgres -
/usr/pgsql-9.0/bin/initdb -D /pgdata
</pre>

Edit the postgresql.conf file (be sure you are still using the postgres user):

<pre>
vim /pgdata/postgresql.conf
</pre>

Update the lines:

<pre>
#listen_addresses = 'localhost' ...
#port = 5432 ...
</pre>

To read:

<pre>
listen_addresses = '*' ...
port = 5434
</pre>

Edit the pg_hba.conf file (be sure you are still using the postgres user):

<pre>
vim /pgdata/pg_hba.conf
</pre>

Update the bottom of the file to read:

<pre>
# TYPE  DATABASE        USER            CIDR-ADDRESS            METHOD

# "local" is for Unix domain socket connections only
local   all             postgres                                trust
# IPv4 local connections:
host    all             power_user      0.0.0.0/0               md5  
host    all             other_user      0.0.0.0/0               md5 
# IPv6 local connections:
host    all             all             ::1/128                 md5
</pre>

Start the server:
<pre>
/usr/pgsql-9.0/bin/pg_ctl start -D /pgdata
</pre>

#### Create users for external access

Create the power_user as a superuser:
<pre>
/usr/pgsql-9.0/bin/createuser power_user
Shall the new role be a superuser? (y/n) y
</pre>

Create the other_user as a non-superuser, who can not create databases or roles:
<pre>
/usr/pgsql-9.0/bin/createuser other_user
Shall the new role be a superuser? (y/n) n
Shall the new role be allowed to create databases? (y/n) n
Shall the new role be allowed to create more new roles? (y/n) n
</pre>

Connect to the database as postgres, and set the new user passwords (Be sure you are still logged in as postgres).  Also create a database for the other_user:

<pre>
/usr/pgsql-9.0/bin/psql -p 5434
postgres=# ALTER USER power_user WITH PASSWORD 'aVerySecurePassword';
postgres=# ALTER USER other_user WITH PASSWORD 'anEquallySecurePassword';
postgres=# CREATE DATABASE other_user WITH OWNER other_user;
</pre>

#### PgAdmin III usage

We can download the latest PgAdmin from the same repositories for Linux, or get the latest from [pgadmin.org](http://pgadmin.org/download/) for Win/OS X.  Be sure that you download a PgAdmin version that is compatible with the 9.0 release.

After installing and launching PgAdmin, use the plug button in the top left to configure a new server connection.  Enter a name to identify your server, the host of your EC2 instance, and change the port to 5434\.  Connect to the Maintenance DB "postgres" as "power_user" with the password you configured.  

Notice that you can also connect as "other_user", but other_user can only see the "other_user" database schema (and not the postgres data).  Also notice that you can not connect remotely as the postgres user.

#### Final thoughts

Now you have a postgresql 9.0 installation on an AWS EC2 instance running Amazon Linux.

A couple of additional notes:  

1.  It is a good idea to set a password for the postgres user, and change the local connection from trust to md5 in your pg_hba.conf file.
2.  It is also a good idea to restrict access to the power_user and other_user to a distinct IP or group of IPs.  Alter the "0.0.0.0/0" value for each user in pg_hba.conf to match the appropriate IP or group.
3.  This service will not start if the instance is restarted.  Run "chkconfig postgresql-9.0 on" (as root) to make it start automatically.

Let me know if I skipped any steps, or if anyone has ideas to make this implementation better.  I hope this helps a few people get PostgreSQL 9.0 up and running, and keeps the connections as secure as possible.  Feel free to post questions in the comments, I will do my best to assist.
