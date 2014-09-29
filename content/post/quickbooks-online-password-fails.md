{
  "title": "QuickBooks Online password fails",
  "description": "QuickBooks Online password fails",
  "date": "2011-08-01",
  "url": "quickbooks-online-password-fails/",
  "type": "post",
  "tags": [
    "security",
    "dev"
  ]
}
I recently started a position with a new company.  I am excited about the opportunity, and look forward to learning new skills and using new software/services.  One particular service that I was happy have an opportunity to use (not because I particularly want to, but rather because so many people/organizations make use of it) is QuickBooks.

I have not gotten very far into the process of using QuickBooks Online, but I must admit that my initial reactions are bleak.

#### Reaction 1: System requirements

[This surprised me a little](http://quickbooksonline.intuit.com/questions-answers.jsp) (you will need to manually expand the system requirements).  Not only is Linux missing, but you are limited to Windows XP or MacOS X.  Apparently there is no support for Vista, and likewise no support for Windows 7\.  They do not specify a version limitation for "MacOS X"; which means I could (in theory) use 10.0 - 10.3\.  However, their browser requirements are Firefox 3.1 or Safari 4.1 (I think you will encounter substantial roadblocks getting these to work in 10.0 - 10.3).  They do allow any version of Chrome for Windows or Mac, I suppose that's their way out of this.  

I wrote about similar ["Technical Requirements" frustrations in the context of BOA over at GeekGumbo](http://www.geekgumbo.com/2010/10/02/bank-of-americamerrill-lynchs-ridiculous-software-reqs/) a while ago.  They are even more draconian about it, making it all the more unnerving.  Anyway, strike one.

#### Reaction 2: Password strength broken?

Hmm.  So I entered my ([KeePassX](http://imperialwicket.com/tags/keepass) randomly generated) password on the account creation screen.  Normally, I do not even look at these passwords, I just generate it, save the KeePass database, and go about copying/pasting the password from one place to another.  But when I start to get weird behavior, I pay a little more attention (this way my rant-y email to customer support can be more targeted).

So Here was my first password attempt:

<pre>
fOxc):nJJ}jY|`_N"{In :S;t
</pre>

Alt+Tab back to the browser window (I am on Fedora 14, and I figured Firefox 3.6 was the safest plan), then a quick Ctrl+V, Tab, Ctrl+V and I'm in business.

![qb_strength.png](/files/qb_strength.png)

Wait, why does my 25 character (upper, lower, number, white space, minus, underline, and special characters inclusive) not register a 5/5 on the password strength?  Browser incompatibility?  Can't be... 

Let's try to create this user anyway.  Well, look at that.  They have determined that my password has a space in it, and they are telling me that I can't do that.  Oh, but the error message only displays when the primary password textfield is active.  That's an interesting (poor) design decision.

Ok, no spaces.

#### Reaction 3: Password length limitations.

Just to put this in perspective, this is how I feel about password limitations:

1.  Stop limiting my password length.  In other words, stop limiting my password length!  What is anyone saving by limiting password length?  I do not think that the difference between 16 and 25 characters (I usually default to 25) is really saving anyone money or development time.  Call it varchar(64) and I will be happy.
2.  If you are going to put password character limitations (character type or number of characters), they damn well need to be on the password creation screen where I can see them.  Don't wait for me to enter my characters and then tell me that I messed up.

Ok, I guess I can forgive you for not allowing spaces.  I don't understand what you gain from this limitation, but I will run with it.

Take two, no spaces.  Still a fine looking password, if you ask me:

<pre>
0Z8-X$I]#63W6lMC"^"y\(j|V
</pre>

Ok, create user - all right, a welcome screen!  Oh wait...

![qb_false-welcome.png](/files/qb_false-welcome.png)

#### Reaction 4: Terrible (and insecure) UI/UX

I acknowledge that I am not the easiest user to please.  I want to use an unpopular OS (Linux), I am likely to use a beta browser, and I want to give you a password that is strong.  But a screen that includes, "Welcome to X" as a header, and then tells me, "We encountered an error processing your [account generation] request. Please try again." is like throwing salt in the stab wound you just placed carefully in my back.  At least they gave me a Back option.

It goes all the way back to the initial welcome screen asking me to create a new user ID, but still keeps my hash id in the GET params.  That's a little strange, I think they should know that I still need to create a new user ID.  Oh well, "Create a new user ID" please.  WOW!  Wait, how do you still know my password (the one that didn't successfully create an account)?  Why is my password still filled in (and confirmed)?  Again, I am probably in a user flow that fewer than 1% of the QuickBooks Online users ever see, but this is where security "Accidents" happen, Intuit.  

Anyway, as my frustrations continue to grow, I start the methodical removal of special characters from my password.

\    -    0Z8-X$I]#63W6lMC"^"y(j|V
"    -    0Z8-X$I]#63W6lMC^y(j|V

I did not try putting the escape slash back into the password, so I can't guarantee that QuickBooks Online is ok with the "\" character, but removing the quotation marks certainly helped.  

#### Takeaway

It is not difficult to test your error handling on account creation screens.  It is even less difficult to declare to your potential users what characters they can and can not include in their passwords.  A simple "No spaces or double quotes in your passwords, and limit them to 6-32 characters" would do nicely.  

I will point out that on the User Info: Change password screen, they tell you "(6 to 32 characters, no spaces)" after the new password box.  Why that is missing on the account creation screen baffles me.  My excitement pertaining to QuickBooks experience is waning, and I will close with:

#### Reaction 5: You must enable popups from this site in your browser

Ugh, really?  Do you want users, or are you trying to push them to your competition?   
