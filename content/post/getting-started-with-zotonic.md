{
  "title": "Getting started with Zotonic",
  "description": "Getting started with Zotonic",
  "date": "2012-05-26",
  "url": "getting-started-with-zotonic",
  "type": "post",
  "tags": [
    "postgresql",
    "erlang",
    "zotonic",
    "crunchbang"
  ]
}
Here are step by step instructions for running [Zotonic (the Erlang CMS)](http://zotonic.com/) on CrunchBang Linux (works for most Debian distros with minimal package and version changes). This is on a vanilla CrunchBang Linux installation, where 'developer' packages are installed during the CrunchBang welcome script. If you didn't install these on launch, you can execute 'cb-welcome' to run through CrunchBang's initial options again and just be sure to install developer-labeled items. If you want to be more cautious about package installation, I think you'll need 'sudo apt-get install gcc g++ make', but that may leave you with a few errors.

### Erlang (from source)

I am not sure which Erlang release is in the Crunchbang/Debian repositories; I am going to build it from source. After installing #! with most of the developer options in cb_welcome:

Install some dependencies we need to build Erlang:
<pre>
sudo apt-get install libncurses5-dev openssl libssl-dev \
  unixODBC-dev openjdk-6-jdk openjdk-6-dbg
</pre>

Download, extract, configure, make, install Erlang:
<pre>
wget http://www.erlang.org/download/otp_src_R15B01.tar.gz
tar xzf otp_src_R15B01.tar.gz
cd opt_src_R15B01
./configure
make
sudo make install
</pre>

### PostgreSQL 8.4 (from repos)

There are some nice enhancements in the 9.x PostgreSQL releases, but they have little bearing on our goal of getting started with Zotonic. As such, we will grab the default PostgreSQL. Currently, 'sudo apt-get install postgresql-9.1' will install the 9.1 release, if you prefer to install and use 9.1.

Install postgresql and create a database and user for Zotonic
<pre>
sudo apt-get install postgresql
sudo su postgres
psql
postgres=# CREATE USER zotonic WITH PASSWORD 'zotonic';
postgres=# CREATE DATABASE zotonic WITH OWNER = zotonic ENCODING = 'UTF8';
postgres=# GRANT ALL ON DATABASE zotonic TO zotonic;
postgres=# \c zotonic
postgres=# CREATE LANGUAGE "plpgsql";
postgres=# \q
exit
</pre>

### Zotonic (from GitHub)

We will install Zotonic from the Git repository. This has a lot of benefits, and also some points of potential confusion. It is quite easy to remain current with a git clone, as 'git pull' will generally work to retrieve any updates. However, Zotonic's master branch is not always stable (which is fine, but can spark issues for some users who expect otherwise).

I'm going to install Zotonic in my home directory. It will run fine wherever you put it, just be aware of the permissions necessary to make updates.

<pre>
cd
git clone git://github.com/zotonic/zotonic.git
</pre>

This clones Zotonic into a ~/zotonic/ directory, and by default we retrieve the master branch. Use 'git branch -a' to show all available branches, and notice that there are remote branches corresponding to several releases. For our Zotonic installation, we will use the latest release branch (currently 0.8.x).

<pre>
cd ~/zotonic/
git checkout release-0.8.x
</pre>

### Zotonic configuration

At this stage, our ~/zotonic/ directory is representative of the 0.8 Zotonic release. We need to 'make' zotonic, and then take care of some initial configuration. We will also first install the imagemagick package - without which Zotonic will encounter issues in its image handling.

<pre>
sudo apt-get install imagemagick
cd ~/zotonic/
make
</pre>

Now we can generate a new site using Zotonic's built in service script (still in the ~/zotonic/ directory):

<pre>
./bin/zotonic addsite -s blog erlang_is_awesome
</pre>

This generates a copy of the 'blog' skeleton site (available in the ~/zotonic/priv/skel/ directory if you want to review it) as a new site in the ~/zotonic/priv/sites/erlang_is_awesome directory. You will notice an initial credentials confirmation message:

<pre>
Database host: 127.0.0.1      # This matches our PostgreSQL installation
Database port: 5432	      # This is the PostgreSQL default
Database user: zotonic        # Accurate if you used the statements above
Database password: zotonic    # Accurate if you used the statements above
Database name: zotonic        # Accurate if you used the statements above
Database schema: public       # Accurate if you used the statements above
Admin password: admin         # Remember this
</pre>

If you used different database credentials, you could be more articulate with the zotonic addsite command. We happened to use the default credentials, but we could have explicitly requested them as:

<pre>
./bin/zotonic addsite -s blog -h 127.0.0.1 -p 5432 -u zotonic -P zotonic -d zotonic -n public -a admin erlang_is_awesome
</pre>

At this point, either add 'erlang_is_awesome' to your 127.0.0.1 line in /etc/hosts, or edit the site config (~/zotonic/priv/sites/erlang_is_awesome/config) to match an existing hosts entry. Note that entering "127.0.0.1:8000" or "localhost:8000" as a site's hostname will cause problems, as requests to 127.0.0.1 or localhost are handled as administrative requests by Zotonic. Speaking of which...

### Multisite Adminstration

Load 127.0.0.1:8000 or localhost:8000, and you'll see a friendly Zotonic admin area. This is not the admin for the site you just created, it is the admin area for the entire Zotonic installation (which can host multiple sites). The admin password for this is available in the ~/zotonic/priv/config file, and can not be changed while Zotonic is running. There are instructions for changing/controlling the password in that file, but it should not be necessary to make changes.

### A few hurdles

Zotonic's 0.8 release made a lot of great updates, and they continue to do so with master and the advance to 0.9\. That said, 0.8 made some changes that may cause more headaches for newcomers than were present in 0.7 (or perhaps I just royally mucked up the install).

Some things I encountered:

Upon first advancing to "erlang_is_awesome:8000/admin" I was greeted with a very basic template - so basic that my first check (before login) was to validate that css was all getting loaded. In 0.7 the default blog template was very Zotonic-y, and consistent with zotonic.com. The updated [lack of] styling is not a terrible thing, as Zotonic promotes custom templating anyway, but to a new user, it just looks like it needs work. When a quick web search for "Zotonic Templates" turns up very little, I see some newcomers jumping ship.

After logging in as admin, I immediately tried to visit my site, only to find what amounts to an error message:

<pre>
You see this page because you didn't enable a site module or the site module doesn't contain the template home.tpl.
</pre>

To Zotonic's credit, the very next line includes a link and the following:

<pre>
Go to the Admin module pages to enable the site module.
</pre>

My concern/question here is, why wouldn't I want the site module enabled, and why can't it be enabled by default? I assume there are some cases where I might not want that, but I'm having a hard time seeing them. When you get to this point, just use the link to go to the Modules page (or navigate to the Modules section in the admin area) and enable the site module that is named consistently your sitename (erlang_is_awesome in this walk through).

Another nit-picky point is on the Modules page, those gray/green circles on the left look like buttons, but they don't do anything. Making this worse is the fact that each record in the table shows a pointer, and links to the page. So you can click all over the place and not accomplish anything. I happened to be working on a small machine and had the browser window shrunk to a width that hid the 'activate' and 'deactivate' buttons on the far right - so my useless clicking seemed like a bug until I performed some horizontal scrolling.

Zotonic is still pre-1.0, and I want to highlight that I've used earlier versions that felt a little cleaner from a CMS-user's perspective. Nonetheless, I want to highlight that while these hurdles were annoying, I don't think they reflect the overall quality and efficiency you encounter using the Zotonic CMS once you're beyond them.

### Making your site

After enabling my site module from the admin area, I was able to view my site. I still needed to manually re-upload some images that didn't seem to copy properly (not sure if this was permissions-related and something that was my fault?). I just navigated to Media -> then the missing image and re-uploaded the two defaultdata images. The originals are available in the ~/zotonic/src/install/defaultdata/blog/ directory.

Add some style customization to your site in ~/zotonic/priv/sites/erlang_is_awesome/lib/css and ~/zotonic/priv/sites/erlang_is_awesome/lib/images. If you need a little more detail, Michael Conners put together excellent [Zotonic template generation instructions](http://michaelconnors.net/en/article/503/zotonic-themes).

There is also a lot of good data in the [Documentation section at zotonic.com](http://zotonic.com/documentation), but it predominantly pertains to development and not so much editing/content creation. This is already getting long, so I'll let this digest, and try to follow up soon with more details about content creation and management.
