{
  "title": "Installing KeePassX on Fedora 14",
  "description": "Installing KeePassX on Fedora 14",
  "date": "2011-02-08",
  "url": "installing-keepassx-on-fedora-14",
  "type": "post",
  "tags": [
    "keepass",
    "fedora"
  ]
}
[KeePassX](http://www.keepassx.org/) is on hiatus as the project moves toward the 2.0 rewrite. In the meantime, pre-compiled packages are becoming difficult to locate. This is not a major issue, as the src files are available for source forge. One hurdle I encountered is that the INSTALL instructions that accompany the latest src files are a little inaccurate for Fedora.  

In order to install KeePassX (0.4.3) on Fedora (I am using 14), perform the following:

1.  Download the src package, link to sourceforge available [here](http://www.keepassx.org/downloads).
2.  Extract the src:
<pre>tar xzf keepassx-0.4.3.tar.gz</pre>
3.  Install the qt libraries (and the hordes of dependencies):
<pre>sudo yum install qt-devel qt-config</pre>
4.  Move into the src directory, install some additional build tools, make, and install
<pre>
cd keepassx-0.4.3
qmake-qt4
sudo yum install gcc gcc-c++ libXmu-devel libXtst-devel
make
sudo make install
</pre>

There you have it, KeePassX 0.4.3 on Fedora 14.
