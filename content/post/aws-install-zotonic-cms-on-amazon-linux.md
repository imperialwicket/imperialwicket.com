{
  "title": "AWS: Install Zotonic CMS on Amazon Linux",
  "description": "AWS: Install Zotonic CMS on Amazon Linux",
  "date": "2011-06-12",
  "url": "aws-install-zotonic-cms-on-amazon-linux",
  "type": "post",
  "tags": [
    "aws",
    "linux",
    "erlang",
    "zotonic"
  ]
}
[Zotonic CMS](http://zotonic.com/) has been around for a little while, and it has been on my developent bucket list for at least a few months.  For me, it really came down to a handful of pros and cons:

Pros:

*   Written in Erlang - fast, modular, functional, capable of doing a lot with comparably little resources

*   Active community

*   Opensource

*   Able to host multiple sites within a single installation 

Cons:

*   Written in Erlang - a language with which I have no experience

*   Requires Erlang - which basically means at least a VPS (or the functional equivalent) if you want to deploy to a live server, shared hosting solutions do not offer Erlang

So, yeah - time to get on that first con.  While I do not have a hosting agreement that supports Erlang, I do have an open AWS account.  And (although it took a little troubleshooting), I finally managed to [install Erlang on Amazon Linux](http://imperialwicket.com/aws-install-erlang-otp-on-amazon-linux) (which is free usage tier eligible).  So, without further ado, here is what I did.

#### Launch an EC2 instance

I have covered starting a basic EC2 instance a couple of times.  The [AWS: Basic LAMP](http://imperialwicket.com/aws-building-a-lamp-instance) article should suffice to get you started if you are new to Amazon Web Services.  A few changes you will want to keep in mind:  

1.  Do not bother installing anything via yum (from the Basic LAMP article).

2.  Be sure to add port 8000 to your security group.

#### Install Erlang

Full installation description and additional details are available in a dedicated article [here](http://imperialwicket.com/aws-install-erlang-otp-on-amazon-linux).  Commands to get it done are as follows:

<pre>
cd ~
wget http://www.erlang.org/download/otp_src_R14B03.tar.gz
sudo yum install gcc gcc-c++ make libxslt fop ncurses-devel openssl-devel *openjdk-devel unixODBC unixODBC-devel
cd ~/otp_src_R14B03
./configure
make
sudo make install
</pre>

#### Install PostgreSQL and create a database for Zotonic

Additional information about these commands and PostgreSQL installation on Amazon Linux is available in [quick and dirty format](http://imperialwicket.com/aws-install-postgresql-on-amazon-linux-quick-and-dirty) and [more thoroughly](http://imperialwicket.com/aws-install-postgresql-90-on-amazon-linux), I am including the quick and dirty installation below.

For those just looking to get PostgreSQL installed to test Zotonic, here is the abridged version:
<pre>
sudo yum install postgresql postgresql-server postgresql-devel postgresql-contrib
sudo service postgresql initdb
sudo vim /var/lib/pgsql/data/pg_hba.conf
[Change all instances of 'ident' to 'trust' - BE CAREFUL WITH THIS IN PRODUCTION]

sudo service postgresql start
psql -U postgres

postgres=# CREATE USER zotonic WITH PASSWORD 'zotonicPassword';
postgres=# CREATE DATABASE zotonic WITH OWNER = zotonic ENCODING = 'UTF8';
postgres=# GRANT ALL ON DATABASE zotonic TO zotonic;
postgres=# \c zotonic
postgres=# CREATE LANGUAGE "plpgsql";
postgres=# \q
</pre>

#### Install Zotonic and supporting libraries

Information related to the latest source and downloads is available at the [Zotonic downloads page](http://zotonic.com/download).  I am installing to /opt/zotonic/ and then changing ownership to the main ec2-user.  This ownership/permissions scheme is not one I would advise using on a production server.

<pre>
sudo yum install ImageMagick
cd /opt/
sudo wget http://zotonic.googlecode.com/files/zotonic-0.6.0.zip
sudo unzip zotonic-0.6.0.zip
sudo chown -R ec2-user:ec2-user zotonic
make
</pre>

#### Configure the default site and start Zotonic

At this point, just edit the "priv/sites/default/config.in" file as recommended on the [Zotonic installation](http://zotonic.com/install) walk-through.  A quick update to the hostname (I used the "ec2-11-11-111-111.compute-1.amazonaws.com:8000" hostname for this test), dbpassword, and the keys is all you need before starting Zotonic.  I tripped at this step (due to lack of attention to detail), and edited "priv/config.in", this results in your default site not deploying, and you simply see a site manager with no sites listed.  Once I reviewed what I was supposed to do, I updated and the default site went live without issues.

Navigate to your page (make sure that your security group includes port 8000), and login using user "admin" and a blank password.  You receive instruction to update the password on the first login.

#### Get started

I have read a lot about Erlang's benefits (speed and memory related) over some of the popular OO languages out there.  Nonetheless, I was astounded at the speed of the Zotonic page loads on an AWS Micro Instance (in debug mode, no less).  Zotonic claims around 10 times faster (and more) speed than PHP content management systems; while I have yet to run a test, I intend to do just that.  I must admit, I expect my tests to confirm their claims.

The Erlang/PostgreSQL requirement will likely push Zotonic out of contention for a lot of individuals who love their inexpensive shared hosting solutions.  However, for freelancers and web development companies hosting their own VPS or handling server hosting agreements for clients, I can see this being a great way to really make the most of your servers.  

The Zotonic team (and at least a couple of users) confirm that [WebFaction](http://www.webfaction.com/) is a great solution.  While I am not inclined to switch hosting providers at this time (and I am currently on shared hosting with limited SSH access and virtually no installation privileges), getting this configured on a free AWS Micro instance was enough to inspire my continued attention to the Zotonic project.  I aim to post more on Zotonic as I test the CMS more (likely using an EC2 Micro instance).
