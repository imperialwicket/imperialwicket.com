{
  "title": "Samsung Captivate: Root and install ROM (Fasty)",
  "description": "Samsung Captivate: Root and install ROM (Fasty)",
  "date": "2011-09-27",
  "url": "samsung-captivate-root-and-install-rom-fasty",
  "type": "post",
  "tags": [
    "captivate"
  ]
}
I wrote about my Captivate trials before [here](http://imperialwicket.com/samsung-captivate-software-update-jh7) and [here](http://imperialwicket.com/samsung-captivate-power-down-issue). In general, I have been less than pleased with the device. Anyway, I finally got around to installing a ROM, and it did not go quite as smoothly as I had hoped. Since I encountered a lot of conflicting information and took a few wrong turns, I wanted to post what worked for me. I am not going to rewrite the instructions, but I am including only the posts that have data I found pertinent.

Note that just like it says on all of the links I am providing, this is a do at your own risk activity. 

#### Root the device

First I needed to root the device. There are many effective ways to do this, and only some require Windows. I used the technique detailed [here](http://androidforums.com/captivate-all-things-root/120616-captivate-root.html).

#### Install ROM Manager and Flash ClockWork Recovery to your device

My Captivate was running the 2.1 update. The first issue I encountered was that many walk throughs require 2.2 as a first step. The 2.2 update from Kies Mini wouldn't work for me, so that was out. The second issue is that most walk throughs require ODIN usage, which is a Windows only application. After reviewing a few steps to accomplish recovery with adb (an android development tool), I encountered a seemingly outdated instructional piece:

[The unlockr's guide for custom ROMs on Captivate](http://theunlockr.com/2010/08/02/how-to-load-a-custom-rom-on-the-samsung-captivate-vibrant/)

Those are the instructions I followed, the rest of my notes are simply tips and information that I used while completing the steps from theunlockr.

#### Install my preferred ROM: Fasty 2.5

After some cautious review, I landed on [Fasty](http://forum.xda-developers.com/showthread.php?p=14748351#post14748351). I need this phone to work and can not afford to be using nightly builds or ROMs that could have issues with core functionality. Originally, I was leaning heavily toward Cyanogenmod, but their Captivate support is still not ready for standard usage (imo). 

The above procedure worked for me, but note that I was using a captivate that had not been previously updated to an alternative ROM. After flashing clockwork recovery to the device, I copied the Fasty zip to my device. I was not having luck copying the zip to my SD card, I had to copy it to the root of the device file system.

I will also note that after pointing my device to a ROM zip and rebooting, the phone took several minutes to boot completely. Subsequent boots were fast; as in 10-15 seconds, instead of the usual 90-120 seconds that it required with the AT&T/Samsung firmware.

#### Install a new Fasty Theme

I also decided to update the default Fasty Theme with the Honey Smoke version. Updating the theme involved the same procedure as updating the ROM, start in recovery mode (which you can do right from the power down menu on Fasty), and select the pre-loaded archive to install. It will update the files appropriate for the theme, and no data is lost.  

#### New Launcher/Home

Once Fasty was in place and themed, I downloaded LauncherPro from the market. I love the levels of customization that are possible in LauncherPro. I periodically try other home applications, but I keep coming back. Other than that, I am pretty content with the Fasty 2.5 defaults.

I hope this saves people a little time, and brings a few more Android devices to the 2.2 arena. Feel free to post questions or issues, but keep in mind that the forums may be more helpful than me.
