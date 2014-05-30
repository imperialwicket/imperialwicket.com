{
  "title": "Searching for a Linux machine (Vendors)",
  "description": "Searching for a Linux machine (Vendors)",
  "date": "2011-07-15",
  "url": "searching-for-a-linux-machine-vendors",
  "type": "post",
  "tags": [
    "opensource",
    "teachingopensource",
    "linux",
    "dev"
  ]
}
_This got pretty long, so I am going to make it part 1 of 3\.  Part 2 will walk through the primary candidate machines and part 3 will cover the final purchase.  After I configure and spend some time with the machine, I will post a review._

I decided that it was finally time.  Instead of getting a hand me down computer, cleaning up the drive for dual booting and/or wiping, and installing my current distribution of choice, I am getting a new machine.  And you know what?  Since I am splurging here, I am going to try to get someone else to install Linux for me.  At the very least, I am going to make sure that someone else is guaranteeing me that all the hardware works with Linux, so I need not worry about such concerns.  

Initially, I was going for a laptop (I ended up ordering a desktop, but we will get there in due time).  I realize that this makes hardware compatibility more of an issue, and that you pay a little more per spec due to the size constraints, but I enjoy the freedom of having a laptop that can easily transport when necessary (even if it is only necessary on rare occasions).  The specifications I wanted were as follows:

1.  i7 quad core
2.  At least 16GB RAM
3.  1920x1200 display
4.  Solid state drive (PCI mini SSD drive + standard drive would be ideal)
5.  Web camera
6.  Digital video out (DVI/HDMI)

What will I do with this machine?  Why do I need these specifications?  I will do whatever I want with the machine, and these are the specs that I want.  Or, less defensively, I will use this machine for web, android, and java development, as well as system administration exercises.  I want to be able to have three or four eclipse instances running - with at least one Android Simulator, possibly a virtual machine or two, GIMP and inkscape, a few libreoffice documents, a couple pdfs, several open terminal windows, a text editor or two, at least one firefox window (with however many tabs I want), and probably three chrome windows (again, with as many tabs per window as I want).  While these are open I want to be able to browse the internet, work on any of the open projects/activities, build/compile projects, install locally/remotely, and generally do whatever I want with the machine - all without noticeable delays.  Is this practical?  Maybe not, but that is my motivation and use case.  

I knew of [System76](http://www.system76.com), [ZaReason](http://zareason.com/), [Emperor Linux](http://emperorlinux.com/), and [Penguin Computing](http://www.penguincomputing.com/).  I do not have any experience with Penguin Computing, nor do I have anything negative to say about them.  However, I ruled them out early, as they really do not cater to private individuals (they are much more business-oriented); they also have no laptop options.  I would love to go for a Penguin Computing desktop, but I was not looking in their price range.  Now down to three possible vendors, I decided to search a bit to see if there were additional vendors worth considering.  

[LXer](http://lxer.com/module/db/index.php?dbn=14) has a decent database of GNU/Linux pre-installed vendors, as does [TuxMobile](http://tuxmobil.org/reseller.html).  As I made my way through these lists, focusing only on companies based in the United States, I slowly but surely found that most of these companies have either a) altered their business plans to offer Linux machines as a minute aspect of their services, or b) developed into such a niche that their machines are hardly configurable, and likely will not suit my needs.  Again, I did not work explicitly with each company on the list, I merely spent time looking at most of the web pages (some of which have disappeared since being added).  After a few days of slowly working my way through those lists, I sat back and decided that it was going to be my initial three companies after all.  

Although the following companies did not suit my needs, I definitely want to give honorable mention to [Eight Virtues](http://www.eightvirtues.com/laptops.html), [Think Penguin](http://www.thinkpenguin.com/), and [Linux Certified](http://www.linuxcertified.com/index.html).  Think Penguin caters to users who require less capable hardware, and based on their website and prices, they do a great job while offering competitive prices.  Linux Certified and Eight Virtues both offer great laptop options, and if you go down one tier to more standard 4GBRAM on i5/i3 (or AMD equivalent) systems, their prices are quite reasonable. After cautious review with a very particular machine in mind, I decided System76, ZaReason, and EmperorLinux were the appropriate targets for my search.

For anyone unfamiliar with these companies, here are my very abridged highlights:

#### [System76](http://www.system76.com)

*   System76 branded equipment
*   Predominantly Intel and Nvidia
*   Exclusively Ubuntu, and only one (usually the most recent) version of Ubuntu

#### [ZaReason](http://zareason.com/)

*   ZaReason branded equipment
*   Predominantly Intel and Nvidia
*   Offers latest Ubuntu (also Kubuntu and Edubuntu), and the most recent LTS versions.  Also ships with the latest Debian, Fedora, and Linux Mint

#### [Emperor Linux](http://emperorlinux.com/)

*   Emperor Linux offers Linux installed on OEM systems - supporting models from Dell, Lenovo, Panasonic, and Sony
*   Hardware varies by manufacturer.
*   Offers latest Ubuntu (also Kubuntu and Edubuntu), and the most recent LTS versions.  Also ships with the latest Debian, Fedora, and Linux Mint

I am saving the actual machine selection details for the next article, but the target machines are pretty obvious from each vendor.  In general, the strengths of each vendor (IMO) are:  

System76 - cost, recent hardware
ZaReason - Distro options, proved hardware (although sometimes dated)
Emperor Linux - Distro options, OEM devices (better access to specs/parts/etc.)

#### System76, ZaReason, and Emperor Linux, Oh my!

System76 almost always comes in at the lowest cost per spec.  However, they often include pretty recent hardware releases.  I'm on the fence regarding brand new hardware.  I like latest and greatest technology, but I get really agitated when I purchase something and find that it has defects or limitations - and this is something that occurs.  I have to admit that while I like Ubuntu, their recent activity has reduced my interest in Canonical's (Shuttleworth's) distribution.  That's a topic for an altogether different post, but I want to make it clear that in all likelihood, I will not be running Ubuntu 11.04 on my machine.  This means that for me, System76's OS limitation is a limitation for me as a potential customer.

ZaReason usually comes in a little higher cost per spec (there are a couple exceptions), and this is mostly because of two factors.  One - their line up is a little older at this point (although they released one new laptop while I was choosing a machine, and another just this week).  And two - they tend to use more industry-proved hardware.  This means that they are defaulting to a slightly older graphics card in many cases, potentially one generation older for the processor, and their drives are using Intel X25 instead of 510s (no real benefit between them, and that is also a top for another post.  [See here for some additional information on the SSD comparison](http://www.tomshardware.com/forum/267081-32-intel-series-series)).  Either way, it sometimes seems like ZaReason is charging close to the same price for technology that was new last year.  Again, I will highlight that this is not necessarily a bad thing in my mind.

Emperor Linux is great because many of us are using products from these manufacturers already (I am typing on a Dell Latitude running Fedora right now).  Emperor Linux takes the hardware and linux support troubleshooting efforts out of the equation, and makes sure that what you order is supported.  They maintain their own Fedora-based distribution, but have no problem installing Ubuntu, Red Hat, CentOS, Debian, SuSE, or Slackware.  They will also configure dual boot for you (right in their standard machine options), and have notes about talking to them regarding custom configurations - citing a recent triple boot machine with two Linux variants and XP).  

#### Linux Laptop Vendor conclusions

So my Linux vendor search (particular to the laptop realm) ended up right about where it began, with three of the major players in the top positions - Emperor Linux, System76, and ZaReason.  I have detailed a little about each here, and will discuss the actual machine selection and configurations I investigated in the next post.
