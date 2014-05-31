{
  "title": "Disable system beeps in CrunchBang (Debain) with Intel sound card",
  "description": "Disable system beeps in CrunchBang (Debain) with Intel sound card",
  "date": "2012-04-22",
  "url": "disable-system-beeps-in-crunchbang-debain-with-intel-sound-card/",
  "type": "post",
  "tags": [
    "linux",
    "crunchbang"
  ]
}
I will probably write about this in greater detail another time, but I finally updated my primary laptop from Fedora 14 to the latest [CrunchBang Statler](http://crunchbanglinux.org/blog/2012/02/08/crunchbang-10-statler-r20120207/) release. 

My biggest frustrations had to do with driver issues, which is strange, as I have not experienced hardware compatibility issues in Linux since installing Dapper on an old Dell laptop. These issues were: broadcom wireless driver support (which is an issue with the 2.6.32 kernel release, not a distro-specific issue), and our title issue with system beeps when running with an Intel sound card.

If you are having issues with system beeps in CrunchBang (with a non-intel card), you should check [framling's recommendations](http://crunchbanglinux.org/forums/topic/8543/turning-off-system-beeps/) in the crunchbang forums. Those instructions removed some of my system beep issues, and in fact they work well for a most non-intel cards. You can also try [nmk's simpler recommendation](http://crunchbanglinux.org/forums/post/130406/#p130406) in that same thread, and it will likely resolve the issue for non-intel cards as well.  

Still beeping at startup and shutdown after these configuration changes? My Dell (with the Intel sound card) was. Until I found [these intel-specific notes](http://forums.debian.net/viewtopic.php?f=5&t=65327#p416250) in the Debian user forums. It seems that the intel drivers have a setting that overrides the pcspkr module, and the default is enabled/on. As enok notes in that thread:

<pre>
# echo "options snd_hda_intel beep_mode=0" >> /etc/modprobe.d/alsa-base.conf
# echo "blacklist pcspkr" >> /etc/modprobe.d/blacklist.conf
</pre>

Those two options are all you need on Intel cards. Note that if you attempted any of the techniques in the other posts, you likely already added the "blacklist pcspkr" line to your blacklist.conf file.

You will probably still get a system beep at the next shutdown, since the new beep_mode configuration won't be loaded. But the next time you boot the machine, you should enter a sweet, blissful, and beep-free environment.
