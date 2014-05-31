{
  "title": "AWS: Install PostgreSQL on Amazon Linux (quick and dirty)",
  "description": "AWS: Install PostgreSQL on Amazon Linux (quick and dirty)",
  "date": "2011-05-31",
  "url": "aws-install-postgresql-on-amazon-linux-quick-and-dirty/",
  "type": "post",
  "tags": [
    "aws",
    "postgresql",
    "linux"
  ]
}
I am going to break this into two articles.  The first article will use yum (and the default amazon repositories) for the installation, all defaults, and the ec2 instance EBS root device.  Some more in-depth installation techniques are in the next post ([Install PostgreSQL 9.0 on Amazon Linux](http://imperialwicket.com/aws-install-postgresql-90-on-amazon-linux)).  It seems likely to me that you want some hybrid of the two versions, but this one will get you up and running in a few minutes.  

The goal is to install a PostgreSQL server on Amazon Linux, and to allow external access to that database server.

#### Launch an instance

1.  Login, navigate to EC2 tab.  Use the "Launch Instance" button to get started.
2.  Choose "Basic 64-bit Amazon Linux AMI"
3.  Generate a single instance, availability zone probably does not matter, and I am going to run with Micro (Micro is likely sufficient for proof of concept, but I would not recommend using it in a production server).
4.  Default "Advanced Instance Options" should be fine.
5.  Give your instance a meaningful value for the "Name" key.  This is a good habit.
6.  Create/Use a key pair for which you have the private key.
7.  Create a new security group - I like to dedicate security groups to server types.  If you already have a 'PostgreSQL' server, use that security group.  Avoid using the 'basic' security group for all of your servers and simply adding firewall rules whenever necessary.
8.  Security group config

    *   Group name: postgresql
    *   Group Description: PostgreSQL database server security group
    *   Inbound Rules: SSH - 22 from 0.0.0.0/0 and PostgreSQL 5432 from 0.0.0.0/0

9.  Launch the instance.
10.  Connect with something like:
ssh -i .ssh/myKey.pem ec2-user@ec2-11-11-11-111.compute-1.amazonaws.com

#### Install and configure the PostgreSQL server

Use yum to install the PostgreSQL utils, libs, and server.  Next, initialize a database cluster and make some necessary updates to the pg_hba.conf file.

<pre>
sudo yum install postgresql postgresql-server postgresql-devel postgresql-contrib postgresql-docs
[Confirm the installation and accept the key]
sudo service postgresql initdb
sudo vim /var/lib/pgsql/data/pg_hba.conf
</pre>

At the bottom of pg_hba.conf, change:
<pre>
# "local" is for Unix domain socket connections only
local   all         all                                       ident
# IPv4 local connections:
host    all         all         127.0.0.1/32          ident
</pre>
To read:
<pre>
# "local" is for Unix domain socket connections only
local   all         all                                  trust
# IPv4 local connections:
host    all         all         0.0.0.0/0          md5
</pre>

Note:  Do not use these pg_hba.conf settings on a production server.

Now edit the postgresql.conf file to get the server to listen for requests from other locations.

<pre>
sudo vim /var/lib/pgsql/data/postgresql.conf
</pre>

Uncomment the line (line 59 at time of writing) with "listen_addresses = 'localhost' ... " and change the value from 'localhost' to '*'.  The file should now read:
<pre>
listen_addresses = '*'             # what IP address(es) to listen on;
</pre>

#### Start the PostgreSQL server

After making the appropriate updates to pg_hba.conf and postgresql.conf, we can launch the server.  If you started the server prior to making the configuration changes, you will need to restart the server before you can connect.

<pre>
sudo service postgresql start
</pre>

Congratulations, you now have a PostgreSQL server up and running.  We just need to set a password for the postgres user, and you will be able to connect to the server from other locations.  On the server, continue with:

<pre>
psql -U postgres

postgres=# ALTER USER postgres WITH PASSWORD 'password';
postgres=# \q
</pre>

#### Final thoughts

This gets you up and running, and externally accessible.  If you are experimenting with PostgreSQL, or need to get a proof of concept up immediately, this may suffice.  I can not stress enough that this configuration is inappropriate for production use.  Some reasons are:

*   We might want to be using PostgreSQL 9
*   The postgres user has remote access (don't do this)
*   The postgresql server is using the default port - probably not a major concern.  While security through obscurity is not a sufficient security implementation by itself, it does not hurt.
*   The entire database is accessible to all users remotely (similar to postgres user having remote access)
*   The pgdata directory is using the EBS root device, if the instance goes down, the data goes with it (unless you manually back it up).  You also can not use that same EBS device on another instance; this makes vertical scaling difficult.
*   We are using the 'trust' option in pg_hba.conf.  As long as you limit it to socket connections, this can be acceptable, but you should really avoid 'trust' - if you want to use it, at least limit it to particular users and be sure it is only available for socket connections.
*   Directory location/size.  Using the default database cluster directory is not a terrible idea, but it should really be on a different device, this allows you to more conveniently scale the size of the database cluster.  Generally, it is nice to move the pgdata directory off the EBS root device, and also to a unique directory (I like /pgdata) - it keeps mount points clean.

I will post a more extensive installation (of PostgreSQL 9.0) later this week.
