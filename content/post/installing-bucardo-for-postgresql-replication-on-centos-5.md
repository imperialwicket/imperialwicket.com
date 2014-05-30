{
  "title": "Installing Bucardo for PostgreSQL replication on CentOS 5",
  "description": "Installing Bucardo for PostgreSQL replication on CentOS 5",
  "date": "2011-06-29",
  "url": "installing-bucardo-for-postgresql-replication-on-centos-5",
  "type": "post",
  "tags": [
    "postgresql",
    "linux",
    "bucardo"
  ]
}
#### Choosing a replication system

I needed to replicate (master-slave) a particular group of tables from one PostgreSQL database server to another.  WAL shipping and integrated PostgreSQL streaming replicatino updates in 9.0 both require full database replication, so they were out.  The primary candidates for replication at the table level are (IMO): [Slony](http://slony.info/), [Bucardo](http://bucardo.org/wiki/Bucardo), and Londiste (part of [SkyTools](http://pgfoundry.org/projects/skytools)).   These are all trigger based replication systems, and the advantages/disadvantages among the three of them are minimal, and seem to depend on fringe case scenarios that are extremely particular to specific implementations.  That said, my decision went like this:

I think that Slony is a pain to administer, particularly if you have to deal with regular addition/removal of tables.  That said, an updated slony_ctl [recently became available here](http://pgfoundry.org/projects/slony1-ctl/) and claims to mitigate these issues.  I will likely give it a try soon.  Also, Slony needs to run on both machines.  Londiste is written in Python, which is not really an issue, except that I am working at a location that does not do a whole lot with Python.  Bucardo is written in Perl, and we happen to have devs who are more familiar with Perl.  Also, Bucardo does not require installation on both servers.  If it is necessary, Bucardo also supports Master-Master replication; this was not an issue for me, but I believe it is the foremost trigger-based replication system that supports master-master.  I do not have a particular interest in one over the other, and I settled on Bucardo mostly because it is written in Perl.

#### Bucardo installation options

Everywhere I reviewed suggested that Bucardo could easily be installed, so I went about making that happen.  As far as Perl goes, CPAN is great.  As far as system administration goes, I find yum and rpms are much easier to maintain.  Since most of the Perl packages are available as rpms, I elected to go this route.  Since I encountered a lot of dead-ends and missing packages (most of which look to be resolved if you are using a RHEL-6 derivative), I wanted to document this.

At a high-level, you need to add the RPMForge extras repository and the EPEL (main) repository, then limit the packages that yum pulls from those repositories.  Tracking down which packages to pull from where was not difficult, but it is time-consuming and a bit of a pain in the ass.  So, here:

#### Add RPMForge Extras and EPEL repositories

Since we are starting the code, first be sure that Perl is up to date.

<pre>
# Update Perl, if necessary
sudo yum update perl
</pre>

And now add the repositories:

<pre>
# Add the RPMForge repository (if not available)
#wget http://packages.sw.be/rpmforge-release/rpmforge-release-0.5.2-2.el5.rf.i386.rpm
wget http://packages.sw.be/rpmforge-release/rpmforge-release-0.5.2-2.el5.rf.x86_64.rpm
rpm --import http://apt.sw.be/RPM-GPG-KEY.dag.txt
rpm -K rpmforge-release-0.5.2-2.el5.rf.*.rpm
rpm -i rpmforge-release-0.5.2-2.el5.rf.*.rpm

# Add the EPEL repository (if not available)
#wget http://download.fedora.redhat.com/pub/epel/5/i386/epel-release-5-4.noarch.rpm
wget http://download.fedora.redhat.com/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm
rpm -i epel-release-5-4.noarch.rpm
</pre>

#### Limit the packages for each repository

In these blocks an ellipsis indicates that I am skipping content that remains consistent, and the [<REPO>] text is present as a reference point.  In *.repo files, the parameter order does not matter, so you can add the necessary data anywhere, although the bottom is conventional.

In /etc/yum.repos.d/rpmforge.repo make the following changes:

<pre>
...
[rpmforge]
...
enabled = 0
...

[rpmforge-extras]
...
enabled = 1
...
includepkgs = perl-DBD-Pg perl-DBI
...
</pre>

In /etc/yum.repos.d/epel.repo make the following changes:

<pre>
[epel]
...
includepkgs=perl-version perl-DBIx-Safe
</pre>

If you want to be cautious, you should use "exclude = perl-DBD-PG perl-DBI perl-version perl-DBIx-Safe bucardo" in the CentOS-Base.repo for both [base] and [updates].  This will ensure that yum does not try to update or install these packages from the CentOS-Base repository in the future.  In practice, it is highly unlikely that CentOS base will ever post a newer rpm than rpmforge or epel, so yum will probably handle this on its own.

#### Install the required packages

Go time:

<pre>
sudo yum install perl-DBI perl-DBD-Pg perl-DBIx-Safe perl-version
</pre>

#### Download and build Bucardo from source

There are Bucardo RPMs available, but only in the i386 RHEL-6 repositories.  Rather than dealing with configuring one i386 repos and the hassle of dependencies between i386 and x86_64, I decided to build this one from source.

Latest source files can be determined on the [Bucardo wiki](http://bucardo.org/wiki/Bucardo#Obtaining_Bucardo).

<pre>
# Download and build it wherever you want, I usually do this in my home directory
cd ~
wget http://bucardo.org/downloads/Bucardo-4.4.5.tar.gz
tar xzf Bucardo-4.4.5.tar.gz
cd Bucardo-4.4.5
perl Makefile.PL
# Ignore warnings regarding the ExtUtil::MakeMaker error version
make
sudo make install
</pre>

#### PostgreSQL PL/Perl

For Bucardo to work, you need to create the PL/Perl language in your Bucardo database.  In order to create the PL/Perl language, you need to install that package.  The Bucardo wiki has a nice installation page that details the command:

<pre>
# This returns "No packages available" on CentOS 5.x
#yum install postgresql-plperl
</pre>

Just like the earlier Perl packages, this rpm is available for CentOS 6, but not for CentOS 5\.  In CentOS 5, you want:

<pre>
sudo yum install postgresql-pl
</pre>

Also, note that "postgresql-pl" is the PL/Perl language package for PostgreSQL 8.1 in the CentOS-Base repository.  If you are using a newer PostgreSQL server, you might need to use the format "postgresql-plperl" or "postgresql84-plperl".

#### bucardo_ctl install

I needed to manually create a directory for the bucardo PID file.  I used the default, so I needed to:

<pre>
sudo mkdir /var/run/bucardo
</pre>

#### Replication

In general, I am pretty satisfied with the Bucardo documentation, and I will let them handle replication instructions.  Start at the "Configuring Replication" section of the [Bucardo Installation wiki page](http://bucardo.org/wiki/Bucardo/Installation) or the "Setup the pgbench databases" section on the [Bucardo pgbench example wiki page](http://bucardo.org/wiki/Bucardo/pgbench_example).
