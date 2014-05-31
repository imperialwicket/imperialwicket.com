{
  "title": "Quick and easy Fedora 14 setup (Gnome Classic)",
  "description": "Quick and easy Fedora 14 setup (Gnome Classic)",
  "date": "2011-07-20",
  "url": "quick-and-easy-fedora-14-setup-gnome-classic/",
  "type": "post",
  "tags": [
    "opensource",
    "teachingopensource",
    "linux",
    "dev"
  ]
}
_What!? Fedora 15 is out you say?  Alas, I am aware, but I can not bring myself to use Gnome3 just yet.  I am hopeful that Gnome 3 will push forward and become a viable replacement for Gnome 2, but I do not think that it has achieved this yet, so, Fedora 14 it is._

Just so it's out there, I spent a lot of time trying to find the Fedora 14 downloads.  I don't know if I missed some blaring download on [fedoraproject.org](http://fedoraproject.org), but I could not find it there.  I found the archives, including up to 13, and the current downloads only show 15\.  Until 14 reaches EOL, you should be able to use this link, and it will take you to a mirror:

[Fedora 14 Live CD](http://download.fedoraproject.org/pub/fedora/linux/releases/14/Live/)

After Fedora 14 reaches end of life, it will show up in the archives and be simple to download from fedoraproject.org, and at that point maybe a Gnome classic spin will be available.  Or maybe I will make my way to xfce or lxde.  Or maybe Gnome 3 will be ready, who knows.

There is plenty of good information out there on installing from the CD.  I will say one thing, if you are trying to install on a new Intel 510 SSD that is connected over the Marvell SATA 6 port, go ahead and move that connector to an old SATA 3 port, Fedora will not recognize it in the SATA 6\.  You will also see a couple of weird errors at boot if you have a BIOS with virtualization enabled.  These slow things down a bit, but do not seem to be a major concern.

Now, my Fedora configuration:

#### Get sudo

From the top left, open Applications -> System Tools -> Terminal.  For fellow mouse-haters out there, remember that Alt+F1 opens the Applications menu, so Alt+F1,UP,RIGHT,UP,Enter gets to the Terminal pretty quickly.  I will reduce this key sequence for the Terminal launch later in the article.  From the terminal, switch to root user so that we can add your user to the /etc/sudoers file.  

<pre>
su root
#password
vi /etc/sudoers
</pre>

Do a search for "(ALL)", and copy the root line, replacing "root" with your username.  Your updated file should have something like:

<pre>
root    ALL=(ALL)        ALL
someusername    ALL=(ALL)        ALL
</pre>

Now you can exit root's shell and use it more sparingly.

Still in the terminal, run the following (these will install a lot of dependencies):

<pre>
sudo yum update
sudo yum install gnome-do gconf-editor alacarte \
    vim \
    pidgin xchat thunderbird\
    inkscape gimp \
    mysql mysql-server mysql-workbench \
    php php-mysql \
    postgresql postgresql-server pgadmin3 php-pgsql \
    git subversion \
    eclipse-jdt 
</pre>

After some explanation, I will get to graphics card drivers, browsers, password management, and a heavy text editor.  First the yum install details, line by line:

#### gnome-do gconf-editor alacarte

[Gnome Do](http://do.davebsd.com/) is a launcher application for Gnome (although it works in many window managers, so long as you install it's host of depencies).  If you are coming from another OS, it's a lot like [autohotkey](http://www.autohotkey.com/) and/or [Enso Launcher](http://humanized.com/enso/launcher/) for Windows; or [Quicksilver](http://www.blacktree.com/) for OS X.  

In conjunction with a good launcher, I like to have full control over what I am launching and also some specific key-bindings for a few specific launch events (notably terminal and nautilus).  gconf-editor is great for a lot of things, but I use it primarily to remove desktop clutter and for configuring metacity key-bindings with a gui.  alacarte adds the "Edit menus" option to the top bar when you right click on a menu item, this lets you change the default command for a program (which is also used by Gnome Do...), allowing you to change your terminal size, or to always launch chrome using incognito mode, etc.  I documented this in [an earlier article on Fedora 14](http://imperialwicket.com/customize-gnome-menu-items-in-fedora-14).

#### vim

I like vim.  Maybe you prefer emacs or nano, or maybe your a die-hard vi fan (in which case, it is already installed).  Anyway, I do not care which is better or more capable.  I like vim, and you are free to prefer whatever editor you like.  All I am doing is installing my preferred terminal text editor.

#### pidgin xchat thunderbird

Just like the text editor, these are areas of <strike>extreme rivalry</strike> personal opinion.  So, once again, I am not saying these are the best programs for these purposes, as there are a many and they each have strengths and weaknesses.  These are my preference, and I am suggesting that you install some mechanism for handling IM, IRC, and email.

#### inkscape gimp

There are a few underdog apps for image editing and creation in linux, but chances are good that you will want to have Inkscape and GIMP for dealing with images.  

#### mysql mysql-server mysql-workbench

This puts the 'M' in your LAMP stack, and gives you a reasonable mechanism for investigating your database via the MySQL Workbench GUI.  I am not a fan of the Workbench, and I tend to strongly favor the older (EOL'd) GUI tools.  The legacy MySQL Administrator will build from source on Fedora 14, but the MySQL Query Browser runs into some dependency issues that cause quite a pain.  I will say that the Workbench adds some nice functionality, notably reverse engineering database diagrams and built in SSH tunneling.  It also removes a number of nice features, and adds a lot of interesting bugs.  Oh well, I digress.

#### php php-mysql

This puts the 'P' (where P = PHP) in your LAMP stack.  P = Python is already installed, unless you need a Python 3 release for some reason.  If that's the case, you probably have your own machine configuration plans.  PHP 5.3.6 is currently in the Fedora repositories.  Php-mysql lets PHP talk efficiently with your MySQL database, and should also install the php-pdo package.

#### postgresql postgresql-server pgadmin3 php-pgsql

This puts the first 'P' in your LAPP stack, but don't think of PostgreSQL as a mere layer in a web stack.  MySQL is capable of some pretty extensive functionality too, but PostgreSQL does so with an adherence to SQL standards that puts MySQL to shame.  PgAdmin leaves a little to be desired, starting from a naming/versioning convention that allows pgadmin3 to be at current version of 1.12\.  Nonetheless, the GUI is handy for some things.  Php-pgsql lets PHP talk with your PostgreSQL database.

#### git subversion

Version control.  Learn it, use it.  I use git and svn regularly.  Maybe you need bzr, darcs, mercurial, whatever.  Cvs is there too, but please consider moving to something that allows a little bit more decentralization.  Svn is even pushing it.

#### eclipse-jdt

An integrated development environment.  This is starting to get a little specialized, as I do a lot of Java development, and like to use Eclipse for some of it (Android, GWT, BIRT to name a few Eclipse-centered interests of mine).  It could be that you need a different eclipse package, a different IDE, or no IDE at all.  

#### Non yum installations

If you have an NVidia graphics card, you may want to replace the nouveau driversj.  You can get the nvidia drivers from their downloads page or from the rpmfusion repository.  [JR at If Not True Then False has excellent instructions](http://www.if-not-true-then-false.com/2010/fedora-14-nvidia-drivers-install-guide-disable-nouveau-driver/) for pulling these from rpmfusion (specific to Fedora 14 - but there are 13 and 15 instructions as well).

I consider multiple browsers a necessity.  My usuals are Firefox (with [Vimperator](http://vimperator.org/vimperator) or [Pentadactyl](http://dactyl.sourceforge.net/pentadactyl/) - and [if you're interested](http://superuser.com/questions/261174/whats-the-difference-between-vimperator-and-pentadactyl)), [Google Chrome](http://www.google.com/chrome?platform=linux), and a [Tor client](https://www.torproject.org/) with built in browser.  There are a lot of others that are all worth while, but these are where I start.

I also consider a [good password manager](http://imperialwicket.com/the-best-and-worst-password-managers) to be a necessity, and KeePassX is my preference.  [Fedora 14 installation instructions available here](http://imperialwicket.com/installing-keepassx-on-fedora-14).

Finally, I like to have a heavywieght editor around.  I would guess that 85% of my edits will happen in vim, gedit, or Eclipse, but occasionally I like be able to open a few files in a heavy, GUI-oriented, text editor.  Preferably something cross platform, in case I want to use it elsewhere.  At the moment, I am waivering between two commercial options [UltraEdit](http://www.ultraedit.com/index.html) (a carry over from my days on Windows, now available on Linux) and [Sublime Text 2 (beta)](http://www.sublimetext.com/) (also recently made available on Linux, still in beta, but very promising).  UE is a little more polished in terms of functionality and carries a few additional features, while ST is more polished in aesthetics and offers features like distration free mode and minimap.

#### Customization

Now it's getting down to personalization: desktop background, Nautilus view preferences, configuring workspaces, and additional key-bindings, very specialized software, etc.  I still want to install Erlang, PostGIS, a couple of Tomcat instances, VirtualBox, and a few other tools that I will use.  These are fairly specific to my needs.  IMO, the bulk of the above installs are things from which many Fedora users can benefit.  

There you have it, I'm just about up and running.  I need to configure my chat, email, irc, etc., but I should have most of the applications installed that I will need to be productive.  Did I miss anything?  
