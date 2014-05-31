{
  "title": "The best (and worst) password managers",
  "description": "The best (and worst) password managers",
  "date": "2010-10-29",
  "url": "the-best-and-worst-password-managers/",
  "type": "post",
  "tags": [
    "security",
    "keepass"
  ]
}
Recently I worked with a client who wanted to secure a number of his servers and services.  I found that he was using a combination of notebooks and Firefox "remembered" passwords as his password management technique.  From the start, I will announce that my worst password manager award goes to the "Firefox remembers my passwords for me" technique (although Firefox isn't the only browser guilty of this...).  For anyone reading this who has not yet seen the light - 

1.  Open Firefox.
2.  Select Tools - Options.
3.  Select the Security tab.
4.  Select the "Saved Passwords" button.
5.  Select "Show Passwords", and confirm that "Yes" you want to show passwords.
6.  Winner!  Security at its finest.

My alternative to this is [KeePass Password Safe](http://keepass.info/).  KeePass is free, open source, available cross-platform, and keeps all of your passwords in a password-protected, encrypted database.  That's right, you still need to remember one password to manage all of your entries, but without that master password, no one can access any of your passwords.  

A couple of other great features:

1.  KeePass allows you to store not just URL, user, and pass.  It also has title and notes column, and all of these columns can be used to sort (except password, and user on some *nix packages).  It also includes a search functionality for filtering your entries by keywords and strings.
2.  You can create groups for organization, and also select icons for quick visual organization of different password types.
3.  Need a password?  Highlight the KeePass entry for which you require the password, and use Ctrl-C to copy it to your clipboard.  Don't worry about the clipboard keeping your password around, it automatically removes the data after a configurable amount of time.  Don't like to use Ctrl-C?  Well, even though you should remedy that situation by learning to love Ctrl-C, you can also configure KeePass to copy password data to the clipboard by double-clicking.
4.  KeePass stores entries in a database, but is not limited to a single database.  This means you can have a work database, and home database, with distinct entries, and distinct master passwords.
5.  KeePass is lightweight, using virtually no resources and requiring almost no storage space.  I have about 200 user/pass entries, where about have include URL data and Notes, and the resulting database is 18KB.  The entire installation directory is around 1.5MB, and a good chunk of that is the uninstaller executable.

KeePass is a great tool.  I keep my multiple KeePass Databases on a portable flash drive, along with the installation packages for Mac OS X, Linux, and Windows, just in case I am using a computer without KeePass installed (and without internet connection).  You can also get versions of KeePass that will run on a flash drive, but this seems like overkill to me for such a small application.  

My one concern with KeePass is that the latest releases use .NET, and hence are not natively cross-platform.  KeePass seems to be aware of the ramifications of their decision, and continues to make their 1.x releases available (and even updates them occasionally).  

If you are not using a password manager yet, you should consider KeePass.  If you have a large number of sensitive passwords stored in your browser, you should consider deleting them, and storing them elsewhere. 

<span class="signature">
// TODO croquet rules, Habari theme(s), GWT/GAE
// --imperialWicket
</span>
