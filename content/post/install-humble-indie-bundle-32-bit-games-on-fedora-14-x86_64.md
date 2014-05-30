{
  "title": "Install Humble Indie Bundle 32 bit games on Fedora 14 (x86_64)",
  "description": "Install Humble Indie Bundle 32 bit games on Fedora 14 (x86_64)",
  "date": "2011-07-28",
  "url": "install-humble-indie-bundle-32-bit-games-on-fedora-14-x86_64",
  "type": "post",
  "tags": [
    "linux"
  ]
}
I am a big fan of the [Humble Bundle](http://www.humblebundle.com/) effort.  They are supporting good causes, promoting independent game development, and helping to bring independent games to Linux. 

That said, a lot of the games they promote have been recently ported to Mac and/or Linux.  As a result, not all of them are quite as optimized as they could be for Linux.  Anyway, I was looking forward to a few of the games from HIB #3, and as I downloaded the installers, I got a lot of this:

<pre>
/lib/ld-linux.so.2: bad ELF interpreter
</pre>

All this is really saying is that you are trying to build a 32 bit program, but you do not have 32 bit libraries.  How do you install these libraries?  Ask yum.

<pre>
sudo yum whatprovides ld-linux.so.2
</pre>

On Fedora you want the glibc package, but for the i686 architecture.  You can request the explicit package returned by yum whatprovides, or just:

<pre>
sudo yum install glibc.i686
</pre>

Now run your installer, and get back to gaming.

[UPDATE]

Unless you really wanted to play those games.  In which case, you also need the i686 architecture SDL and SDL_mixer.  If you load HammerFight (on 64 bit Fedora, at least), it tries to launch, and silently fails.  Awesome.  I then tried Cogs, which gives you the following error:

<pre>
Couldn't initial SDL: No available video device
</pre>

Funny.  I could swear I am reading this output on one of my available video devices.  Checking the SDL on 64 bit Fedora, sure enough version 1.2 is installed by default.  I tried adding the devel packages, but this did not seem to help.  Next game, VVVVVV.  After extracting the tarball, I am greeted pleasantly with executable VVVVVV_32 and VVVVVV_64\.  Nice, I will just run this, and:

<pre>
./VVVVVV_64: error while loading shared libraries: libSDL_mixer-1.2.so.0: cannot open shared object file: No such file or directory
</pre>

All right, this is getting a little old.  

<pre>
yum whatprovides libSDL_mixer-1.2.so.0
</pre>

So the 64 bit VVVVVV script wants to use the 32 bit SDL mixer (which requires the 32 bit SDL)?  Whatever...

<pre>
sudo yum install SDL_mixer.i686
</pre>

Still no luck.  I stumbled into this, and thought it would be worth a shot:

<pre>
sudo yum install SDL_image.i686
</pre>

And Crayon Physics wanted:

<pre>
sudo yum install mesa-libGL.i686
</pre>

One more thing:

<pre>
 xorg-x11-drv-nvidia-libs.i686
</pre>

Cogs works.
