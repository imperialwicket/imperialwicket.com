{
  "title": "AWS: Install Erlang/OTP on Amazon Linux",
  "description": "AWS: Install Erlang/OTP on Amazon Linux",
  "date": "2011-06-11",
  "url": "aws-install-erlang-otp-on-amazon-linux/",
  "type": "post",
  "tags": [
    "aws",
    "linux",
    "erlang"
  ]
}
I could not find suitable rpms, and since building was sort of a pain, I thought I would put this together.  This is not an altogether foreign installation, but there are some tricky dependencies, and a few random devel packages that you need.  Start by figuring out the path to the latest [source file](http://www.erlang.org/download.html) download, and use wget:

<pre>
cd ~
wget http://www.erlang.org/download/otp_src_R14B03.tar.gz
</pre>

#### Required Packages Installation

Install the build tools and required libraries.

In case anyone gets to this page while chasing errors (these are particular to amzn-main/amzn-updates repositories, but may be of use for RHEL-based distributions):

*   *openjdk-devel - This avoids the "jinterface : No Java compiler found", applications disabled message
*   unixODBC-devel - This avoids the "odbc : ODBC library - link check failed", applications disabled message

Note that the wxWidgets Warning will display, since the primary usage for my Amazon Linux Erlang installation is web services ([ejabberd](http://www.ejabberd.im/), [Zotonic CMS](http://zotonic.com/), etc.), I am comfortable ignoring this.  If you are installing for local application development, you will likely want to build [wxWidgets from source](http://downloads.sourceforge.net/project/wxwindows/2.8.12/wxWidgets-2.8.12.zip) (requires GTK libs), or install the [erlang-wx from an rpm](http://pkgs.org/search/?keyword=erlang-wx&search_on=smart&distro=0&arch=32-bit&exact=0) or alternative repository (a RHEL6 rpm will likely install without issue).

<pre>
sudo yum install gcc gcc-c++ make libxslt fop ncurses-devel openssl-devel *openjdk-devel unixODBC unixODBC-devel
</pre>

#### ./configure, make, make install

The latest Erlang/OTP source available is R14B03 - update the directory appropriately as this changes.  On a micro instance, configure requires around a minute or two and make took almost 50 minutes.

<pre>
cd ~/otp_src_R14B03
./configure
make
sudo make install
</pre>

#### Use you some erl

Just to be safe give 'erl' a spin:

<pre>
$ erl
Erlang R14B03 (erts-5.8.4) [source] [64-bit] [rq:1] [async-threads:0] [hipe] [kernel-poll:false]

Eshell V5.8.4  (abort with ^G)
1> 
</pre>

You can use Ctrl+G, then 'q' or Ctrl+C then 'a' to quit the shell.

Now you can [get started with Erlang](http://www.erlang.org/doc/getting_started/intro.html) or even "[learn you some Erlang (for great good!)](http://learnyousomeerlang.com/content)".  I plan to try my hand at both.
