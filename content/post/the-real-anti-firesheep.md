{
  "title": "The real anti-Firesheep",
  "description": "The real anti-Firesheep",
  "date": "2010-11-16",
  "url": "the-real-anti-firesheep/",
  "type": "post",
  "tags": [
    "security"
  ]
}
To be blunt, you should be familiar with Firesheep.  If you are not, check out the first few results [here](https://duckduckgo.com/?q=firesheep).  Basically, the utility was a last-straw, final effort to highlight how insecure most of the sites we use really are (those are my words, read the author's at [codebutler.com](http://codebutler.com/firesheep)).  A simple browser plugin, able to sniff out cookies and login requests on the local network, and store appropriate data.  And for those of you who are just reading about Firesheep, or are not familiar with its techniques, don't be fooled by the sites highlighting that it only happens on public WiFi, there's no reason that you can't sniff network traffic on your non-wireless, work network.  In fact, if you have admin access to your machine, you should download [Wireshark](http://www.wireshark.org/) and start listening on IM conversations that people are having (How do you feel about privacy now?).

I don't think it has been more than about two weeks since Firesheep became available, and no major web site has taken a step to secure its traffic (to my knowledge).  What is even more baffling than this lack of responsiveness from web sites who are constantly claiming concern for user privacy, is the fact that so many anti-firesheep hacks, plugins, and 'fixes' are springing forth.  If I understand these 'fixes' correctly, the logic goes something like this:  

The Problem:  Facebook doesn't encrypt data during login procedures, and someone released Firesheep to highlight how easy it is to hack into an account when this implementation exists.
The 'Fix':  Download some other third-party utility to keep Firesheep from being able to sniff out your data.  

I don't even know what to say.  

Analogy time: You find out that your car manufacturer is using door locks that can be easily opened with a small bit of knowledge about the lock.  You don't worry about it, because who has that knowledge?  A third party, in an effort to spark action from the car manufacturer, announces that they'll give you a pre-fabricated utility that opens any car from that manufacturer.  Now you're a little concerned.  What do you do?  You wait until some other third party comes up with an add-on for your locking mechanism that foils the break-in attempts of the pre-fabricated opener?  Are you kidding me?  If this happened, you would demand that the car manufacturer replaced the locks.  

No one would stand for this if it involved real, tangible possessions.  But apparently, virtual possessions are not of critical concern to most, and they just continue on their merry way, providing access to their accounts to anyone who has the motivation to listen.

Since so many of you seem to be missing the point (and my apologies to the informed who may be reading), I'll spell it out.  The real anti-Firesheep is to stop using the insecure sites.  The problem is not Firesheep, it's the sites to which Firesheep can easily acquire access.  If the sites lose enough traffic, and can justifiably attribute that loss to lacking security implementation, then said sites will update their security.  This isn't rocket science, nor is it an unheard of business model.  

I don't use Facebook (certainly not the only problem site, but a big player); it has smelled of deception since it's third privacy policy update.  For all of the uproar that occurs when someone finds out that other users can see their images, I just can not fathom why there is not more concern over the fact that someone can easily login to your account.  

Again, for those missing the point of Firesheep, these major web sites that hold untold amounts of your personal data, have made the simple business decision that the security of your account is not important enough to warrant the necessary investment in a proper implementation.  And hence, your account login will remain open for hacking.

Keep on installing your third-party, anti-Firesheep plugins, though, I'm sure that will get Facebook (and others) to change their implementation.  
