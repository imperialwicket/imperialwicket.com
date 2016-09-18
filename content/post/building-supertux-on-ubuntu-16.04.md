{
  "title": "Building SuperTux on Ubuntu 16.04",
  "description": "Building Supertux on Ubuntu 16.04",
  "date": "2016-09-18",
  "url": "building-supertux-on-ubuntu-16.04/",
  "type": "post",
  "tags": ["linux","supertux"],
  "draft": false
}

I have a couple roommates (read: *children*) who recently developed an interest in SuperTux. With 0.4.0 available in the Ubuntu repositories, we were quickly up and running with the core levels and many of the excellent community contributed levels. Eventually, I let it slip that you could create your own levels, and the fun really started. There's a C# editor that you can run and install on linux via mono. There's a lot to install, but it's pretty functional once you have the build dependencies figured out. After some additional digging I found out that the 0.5.0 release candidates include a new embedded editor. While the release candidate editor is still in need of a lot of polish it's in a reasonable place and requires far fewer dependencies to get up and running. Bonus: you get to play the release candidate game and levels!

## Building SuperTux on Ubuntu 16.04

After some digging, I wasn't able to find an explicit set of dependencies for Ubuntu 16.04, so here's what I ended up with:

````
sudo apt-get install build-essential cmake libsdl2-dev libsdl2-image-dev libglew-dev libopenal-dev libboost-filesystem-dev libvorbis-dev libogg-dev libcurl4-openssl-dev

git clone https://github.com/supertux/supertux.git
cd supertux
git submodule update --init --recursive
mkdir build
cd build
cmake ..
make
````

At this point you have a supertux/build/supertux2 binary from which you can play the game. If you want to install this version you use `sudo make install`; but you likely need to remove an existing supertux package (or customize the installation directory).

That worked for me, I'll be building some levels and may put together some editor tips/tricks if I come up with anything. Happy SuperTuxing!
