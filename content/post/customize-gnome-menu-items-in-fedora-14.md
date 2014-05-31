{
  "title": "Customize Gnome Menu Items in Fedora 14",
  "description": "Customize Gnome Menu Items in Fedora 14",
  "date": "2011-02-05",
  "url": "customize-gnome-menu-items-in-fedora-14/",
  "type": "post",
  "tags": [
    "linux",
    "gnome"
  ]
}
The frustration that led to this post:  I like the Chrome browser.  I do not use it exclusively, and can not say that I would recommend using any browser exclusively.  However, I do think Chrome belongs on your browser tool-belt.  The primary reason that I like Chrome is for web development, which benefits greatly by using Chrome with incognito mode - which ensures that client-side caching issues are avoided.  Chrome launches in standard mode by default, however, my preference is to simply launch it as an incognito window, all the time.  This is usually as simple as changing the menu command, but when configuring a Fedora 14 installation, I found the menu alterations to be strangley difficult to achieve.  Note that this is a Gnome-specific update, not really Fedora-specific.  However, I think Ubuntu up to 10.10 is still shipping with alacarte, and I am guessing that a fair number of the Gnome users are either in Fedora or Ubuntu.  If that guess is accurate, then a lot of the Gnome users who are trying edit their menus, and not finding the "Edit Menus" option, are likely to be using Fedora.  

When searching, you still find a number of posts about right-clicking the menu in Fedora and using the "Edit Menus" option to configure and customize the menu options.  This would work fine, if Fedora still included the package that allows gnome menu editing (I assume the package used to ship with the distribution disc).  While this update is straight forward, it is not as simple as: 
<pre>
# DOES NOT WORK...
sudo yum install gnome-menu-editor
</pre>

Instead, you need the "alacarte" package.  I am not sure which memo I neglected to read, but I have a hard time figuring out the natural connection between "menu editor" and "alacarte".  Nonetheless, that is the package you need.

While you are installing helpful configuration packages, install gconf-editor.  gconf-editor will let you use metacity for OS-wide key-bindings (without some of the limitations of the System->Preferences->KeyboardShortcuts option), and also allows you to edit otherwise hidden application preferences with its GUI (like removing your Trash, Home, and Mounted drives from your desktop in nautilus - I really enjoy a pristine desktop).  gconf-editor comes with a huge number of configuration options, but those are the two I most often find myself using.

So, instead of trying hopelessly to install some derivation of 'gnome-menu-editor', install these:

<pre>
sudo yum install alacarte gconf-editor
</pre>

Now, after restarting your window manager (easiest way is to log out and log back in), you can right click on the menu panel and see the "Edit Menus" option.  From there, you can edit the Chrome menu option to include <pre>-- incognito</pre> at the end, and after another window manager restart, you have a Chrome installation that will launch from the menu system in incognito mode.  The best part about editing the menu item, is that GnomeDo uses the menu system's launch command, so GnomeDo will also launch a window in incognito mode, by default.  

If you want to get really crazy, you could set up two Chrome menu items, one for incognito, and one for standard.  Just be sure to title them distinctly enough that GnomeDo quickly identifies them (use Chrome and inChrome, as opposed to Chrome and ChromeIn).  Or you could stay on the sane side of things, and launch your incognito window, and simply open a standard window with Ctrl+N from there.  

Hopefully this saves some digging through old wikis and forums.  As of writing, menu editing is accomplished with the alacarte package.  That is all you need.
