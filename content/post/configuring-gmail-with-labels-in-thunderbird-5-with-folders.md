{
  "title": "Configuring GMail (with labels) in Thunderbird 5 (with folders)",
  "description": "Configuring GMail (with labels) in Thunderbird 5 (with folders)",
  "date": "2011-08-09",
  "url": "configuring-gmail-with-labels-in-thunderbird-5-with-folders/",
  "type": "post",
  "tags": [
    "opensource",
    "linux"
  ]
}
I do not know why this is so difficult. I have multiple GMail accounts, and dealing with multiple accounts within GMail has always been a weak point (IMO). I first gave Evolution a try, but quickly alterred course when I realized how slowly Evolution loads IMAP messages (is this particular to GMail?). After scouring the internet to find [64 bit Thunderbird for Linux](http://www.imperialwicket.com/x86_64-64-bit-thunderbird-and-firefox-from-mozilla), or 64 bit Thunderbird at all, really, I set about to get things configured.

I thought the requirements were simple. I have several mailing lists and a few project-specific automated messages that I wanted in folders. I want sent mail and drafts to work, and I don't want new messages to show up in multiple places. This is how I accomplished it.

#### Gmail account config

All of this is under the Gear - Mail Settings options.

1.  On the Labels tab, create new labels - these will be your folders on the Thunderbird side.
2.  While you are on the Labels tab, go ahead and hide "Important" and "All Mail", also make sure to uncheck the "Show in IMAP" option on the right. It defaults to checked, but be sure that your custom labels have a check in the "Show in IMAP" option. I usually hide the "Starred" label/folder as well, but I know some users that like to have starred available alongside custom folders.
3.  Moving over to the Filters tab, create new filters. After naming and setting criteria, be sure to check "Skip the Inbox (Archive it), and then apply the appropriate label.
4.  I believe the IMAP config is accurate by default, but on the Forwarding and POP/IMAP tab, be sure that IMAP is enabled and configure the expunge/archive functionality to your liking (just be careful here, a lot of people encounter strange issues when changing these settings).

#### Thunderbird 5 config

At first, I was impressed here.  I am configuring a gmail business email account, so the domain is not blah at gmail dot com, Thunderbird still identified as a gmail address and configured incoming and outgoing servers correctly. Not only that, but the old Thunderbird 2 "feature" where you need to enter your password and save it twice (once for incoming and once for outgoing) is no longer featured (I think they updated this in Thunderbird 3, though).

After some initial pleasure driven by the lack of manual configuration, I quickly realized that my emails were showing up in multiple places (The multiple places issue is resolved by ensuring the "Show in IMAP" boxes are unchecked for some of the default GMail labels). I also wasn't getting information about my custom labels (the folders did not show up, even when manually "Get Mail"-ing). More internet digging...

Account settings -- Server Settings -- Advanced: uncheck the "Show only subscribed folders" option.

Great! Now I get my custom folders (labels from GMail).  A day later, I still wasn't seeing much action in my custom folders.  Thinking that I would spur one of them into action, I selected one of my custom label folders (non-bold, no new messages).  What do you know?  Upon selecting, Thunderbird loaded messages from the server.  Why wasn't this folder syncing properly?

I found this only after pulling my hair out for a little while and randomly clicking on things.  Highlight the folder name in the folder pane, select Edit -- Folder Properties (alternatively, right click on the folder name, and select properties - I can not figure out a way to do this that allows manipulation for more than a single folder at a time, tips welcome [UPDATE: [Thanks to Dragos in the comments for a workaround](http://imperialwicket.com/configuring-gmail-with-labels-in-thunderbird-5-with-folders#comment-11024)]).  In the Folder Properties: General Information tab, check the "When getting new messages for this account, always check this folder" option.  Enjoy your _simple_ IMAP folder configuration in Thunderbird 5 and GMail.

[UPDATE]
If you configure the account in Thunderbird, and then uncheck the "Show in IMAP" option in GMail, Thunderbird will throw errors.  You won't be able to unsubscribe from the folder at that point in Thunderbird, because it populates its subscription list based on what is available on the server, not based on the folders to which you are already subscribed (which makes sense, but can be another annoyance if you are dealing with this setup).  Just re-enable those folders in the GMail settings, unsubscribe to those folders in Thunderbird, and then re-disable the "Show in IMAP" option again in GMail.

Am I the idiot here, or is this particular integration overly complex?

[UPDATE 2]
I did not notice at first, but my Drafts were getting saved to my [Gmail]->Trash folder. This did not really bother me much, since Gmail keeps trash messages around for 30 days, and I can not think of a reason to keep a draft around for that long. However, if you want draft messages to save in the [Gmail]->Drafts folder, proceed to Account Settings -> Copies & Folders -> Under Drafts and Templates: select Other, then <your email> -> [Gmail] -> Drafts.  
