{
  "title": "x86_64 (64 bit) Thunderbird and Firefox from Mozilla",
  "description": "x86_64 (64 bit) Thunderbird and Firefox from Mozilla",
  "date": "2011-08-05",
  "url": "x86_64-64-bit-thunderbird-and-firefox-from-mozilla/",
  "type": "post",
  "tags": [
    "opensource",
    "linux"
  ]
}
_If you don't care about how hard it is to find these, and just want to get the archives: 

*   [Firefox 5.0.1 Linux x86_64](ftp://ftp.mozilla.org/pub/firefox/releases/5.0.1/linux-x86_64/en-US/)

*   [Firefox 6.0 Linux x86_64](ftp://ftp.mozilla.org/pub/firefox/releases/6.0/linux-x86_64/en-US/)

*   [Thunderbird 5.0 Linux x86_64](ftp://ftp.mozilla.org/pub/thunderbird/releases/5.0/linux-x86_64/en-US/)

*   [Thunderbird 6.0 Linux x86_64](ftp://ftp.mozilla.org/pub/thunderbird/releases/6.0/linux-x86_64/en-US/)
I'm linking to the latest en-US, non-Beta releases at time of writing. I do not plan to update this, but the ftp is well organized, once you find it._

Let's cut to the chase: check out Mozilla's default offering to 64 bit Linux users:

#### Thunderbird

![thunderbird-download.png](/files/thunderbird-download.png)

#### Firefox

![firefox-download.png](/files/firefox-download.png)

Just to be clear, I am not doing any strange trickery here. When I navigate to [mozilla.com](http://www.mozilla.com/), the firefox image is what I see. No magical user-agent masking or magic, either:

<pre>
Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/535.1 (KHTML, like Gecko) Chrome/13.0.782.107 Safari/535.1
</pre>

Likewise, if I load [mozilla.org/thunderbird](http://www.mozilla.org/thunderbird) I get the Thunderbird image above. This is not some strange user flow bug either, I can get to the download page through the main mozilla.org site, or any other random and/or redirecting links. All signs point to i686.

So, after installing a lot of frivolous i686 libraries on my 64 bit Fedora installation, I was digging around in the Fedora Forums looking for details on some such library, and I [stumbled over this](http://www.johannes-eva.net/how-to-install-firefox-on-ubuntu-linux). Ok, ubuntu-specific? No, not really. But no real mention of x86_64... wait! There it is, a link to the Mozilla FTP, where 64 bit builds live. 

And there they are. Buried in a land where search engines apparently never wander.  Well, search engines occasionally wander here, so hopefully I can offer a door to 64 bit Mozilla applications for the Linux masses.  Just to clarify, when I started searching for 64 bit versions, I get the blaringly unsupported Windows binaries, forum posts that talk about the fact that there is no official 64 bit build, and a lot of downloads from places like start64.com - which looks a little shady to me.

There are lots of good instructions for installation once you have the archives (including the one linked above at johannes-eva.net that is not actually ubuntu specific). But for some reason, it was overly difficult to actually find the 64 bit archives. Again, here they are:

#### [Firefox 5.0.1 Linux x86_64](ftp://ftp.mozilla.org/pub/firefox/releases/5.0.1/linux-x86_64/en-US/)

#### [Thunderbird 5.0 Linux x86_64](ftp://ftp.mozilla.org/pub/thunderbird/releases/5.0/linux-x86_64/en-US/)

#### Post Script

I just noticed something interesting. The screenshots I provided above, and some of the mentioned URLS were pulled with Chrome 13\. I just loaded the Firefox download page in Firefox, and it displays differently, making it even less apparent which release I am downloading (it is still the 32 bit archive).
