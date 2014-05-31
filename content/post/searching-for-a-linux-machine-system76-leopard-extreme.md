{
  "title": "Searching for a Linux Machine (System76 Leopard Extreme)",
  "description": "Searching for a Linux Machine (System76 Leopard Extreme)",
  "date": "2011-07-23",
  "url": "searching-for-a-linux-machine-system76-leopard-extreme/",
  "type": "post",
  "tags": [
    "opensource",
    "teachingopensource",
    "linux",
    "dev"
  ]
}
_This is part 3 of 3 in a series of articles detailing my effort in purchasing a machine with Linux pre-installed. [Part 1 details the vendors](http://imperialwicket.com/searching-for-a-linux-machine-vendors) I established as serious contenders for my purchase, and some other great vendors that are worthwhile if you have different machine requirements than I did. [Part 2 covers the laptops I considered, and details the decision process](http://imperialwicket.com/searching-for-a-linux-machine-laptops).  I also spoiled the purchase surprise in part 2, but this is Part 3 where I want to detail a little bit more about the purchase and my initial reactions after about two weeks of usage._

#### System76 Leopard Extreme

So I ended up with a [Leopard Extreme](http://www.system76.com/product_info.php?cPath=27&products_id=116).  As it turns out, I probably should have waiting about a month to make my purchase.  Not only are new laptops out that might have changed my decision, but a lot of the components are cheaper.  That's sort of how technology goes.  

Recapping the specs:  I went with the i7 i960 processor, now the default processor (it was an upgrade a few weeks ago when I ordered - such is life).  I kept the RAM at default 6 GB (Crucial Ballistix), as I found the [Corsair Vengeance 24GB](http://www.newegg.com/Product/Product.aspx?Item=N82E16820145350) for cheaper (it's now down to $229, I ordered it at $279).  The hard drive is a 120GB Intel 510 SSD.  I added an optical drive, and I picked up the wireless card just in case I needed it.  

I then ordered the RAM, a monitor, and some speakers from newegg.  My total cost with shipping was right around $2275\.  

Adding perspective on this purchase, I spec'd an Alienware Area-51 from Dell, and brought it to similar hardware capabilities (12 GB RAM - their max, i7 i960, 256 SSD - their only SSD option, and a Tri-CrossFireX AMD Radeon HD 6950 graphics card).  I ended up with a much better graphics card, since Dell equates performance with gaming.  Anyway, long story short - $4649.00\.  When you consider the fact that I have an extra 6 GB of RAM to sell or use as backup, it's probably safe to claim this is more than double the cost of my System76 Machine.  Oh wait, I forgot to mention - I get a license for Windows 7 64 bit too, how delightful!  

As a reminder for anyone who gets here thinking about a Linux machine, the [Fortis Extreme from ZaReason](http://zareason.com/shop/Fortis-Extreme.html) ends up very close in price for very similar hardware.

#### First Impressions

It has been a while since I have had a desktop, and this case is mammoth.  My first reaction was nearly disbelief, how is this thing so large?  I cracked it open, removed some packaging that System76 put in place for safe traveling, and replaced the memory (6 GB of Ballistix out, 24 GB of Vengeance in, and an ear to ear grin for me).  Plugged in some cables, flip some power switches, and go time.  I have to admit, startup time is slower than I expected.  Using the Ubuntu 11.04 that came default (after the initial launch which included some user configuration and install completion steps), startup was around 30 seconds.  With the SSD and a lot of power behind it, I was thinking more about the 15-20 second mark.  I still think I can shave some time off of this with BIOS mods, but I have not looked into it yet.  Since it is a desktop, the restarting/resuming time is less critical than that of a laptop, and it will likely be a while before I am inspired enough to tackle the BIOS modifications.

#### Unity or Gnome 3 or ?

You can read related details on my selection of Fedora 14 for the OS on this machine [in a previous post](http://imperialwicket.com/quick-and-easy-fedora-14-setup-gnome-classic).  I spent several hours with Unity (admittedly not enough to get a great feel for it), and decided that it was just too mouse-centric.  There are a few updates that cater to the keyboard-loving crowd, but it does not seem to be a central theme.  It also felt as though lots of decisions were made for me in Unity.  I think part of this was intentional, and part of it was the fact that this is the first live release.  

So, I switched to Gnome 3, thinking back (fondly) to all those early screenshots and some tinkering I did in the beta stages.  As it turns out, Gnome 3 took a few turns in alternate directions.  I sort of got the feeling that Gnome 3 made a few design changes in response to being replaced by Unity as the default Ubuntu window manager.  This did not make me happy, but it didn't make any final decisions.  What did solidify my decision was lacking Gnome Do and metacity functionality in Gnome 3\.  I like my keyboard shortcuts and I like my launcher.  With that, I started looking elsewhere.  First to Ubuntu 11.04 with XFCE or LXDE, but that led me to the XFCE/LXDE Fedora spins.  Then I thought about KDE for a while.  This is the investigation that led me to awesome - which I will likely try soon.  Anyway, after two days of trying live iso releases of various distributions using various window managers, I decided to dig up Fedora 14, and stick with Gnome 2 on a distribution with which I am familiar.

After my multiple day investigation in window managers, I can say three things about both Unity and Gnome 3:

1.  They both have the potential to offer a smooth and highly-usable experience
2.  They both have a beta feel to them.
3.  They both have a target demographic that does not include me.

I am not quite ready for a mouseless machine - but I have to say that [awesome](http://awesome.naquadah.org/) has caught my attention as a possible next step in my X window journey.  I have a feeling that when Fedora 14 and Ubuntu 10.04 LTS reach EOL, I will be using either XFCE, LXDE, or awesome.  I may even jump ship from Fedora and Ubuntu, only time will tell.

#### The headaches

I don't mind the driver issues, hardware compatibility investigations, and general tinkering that is necessary at times to get Linux working how you want it.  However, by the end of the four days I spent 'tinkering' with this machine, I was wondering about saving $200 with System76, when I could have received a similar machine with Fedora (Mint, any major Ubuntu release, or any major Ubuntu release LTS version) already installed.  I certainly don't regret the purchase, and I am happy that I have the newer RAM, motherboard, and SSD.  Now I just need the kernel and distributions to catch up with the technology so that I can use it all.  I will throw in a list of the more frustrating issues I encountered (in no particular order):

*   After deleting Unity from Ubuntu 11.04, my nvidia drivers/config were hosed.  I would try to login, and see a gnome error screen that took me back to the login.  I did not figure out what was wrong, as this solidified my desire to install a different distro or at least a different Ubuntu release/spin.
*   Fedora 15 does not place the install to hard drive icon in plain site, you must dig through the menus to find it.  I also got the nvidia driver issue after the Gnome login screen with Fedora 15\.  Likely a Gnome 3/nvidia issue?
*   Fedora will not recognize a hard drive connected to a SATA 3 @ 6 Gb/s port.  I had to move the SATA connector from the blue 6 Gb/s port to a black 3 Gb/s port.  I doubt this had much impact on performance, but it was a little bit of a hassle figuring out why Fedora didn't see a hard drive.
*   The nouveau drivers almost worked in Fedora 14, but I got weird artifacting in browser windows, so I added the nvidia drivers
*   The nvidia rpm install doesn't work quite right in Fedora 14 ([fix this here](http://www.if-not-true-then-false.com/2010/fedora-14-nvidia-drivers-install-guide-disable-nouveau-driver/))

That's about it.  No major issues, but after several days of playing with distros and window managers, I am happy to be back on something functional and familiar.  Overall, great job to System76\.  I got a great deal on hardware that works with Linux.  I think System76 should at least offer the option of installing the latest LTS release of Ubuntu, but I can understand standardizing on the latest release.  Keep up the great work System76\.  I can't say I'm a die hard fan, mostly because I am not excited about the direction that Ubuntu seems to be headed, but the fact that I can get a reliable and cost-effective Linux machine is great.  I hope ZaReason/System76 and all the other Linux machine sellers/resellers continue to spur each other on; it is great to be able to acquire affordable machines with Linux pre-installed.  
