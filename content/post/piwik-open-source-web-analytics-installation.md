{
  "title": "Piwik (Open Source Web Analytics) Installation",
  "description": "Piwik (Open Source Web Analytics) Installation",
  "date": "2010-10-17",
  "url": "piwik-open-source-web-analytics-installation",
  "type": "post",
  "tags": [
    "habari",
    "piwik"
  ]
}
Slowly but surely, I'm transitioning away from using Google services for everything that I do.  I'm not offering ads on imperialwicket.com at the time of this writing, but if I do, I don't think I will want to use Google AdSense.  In my search for suitable alternatives, I encountered [OpenX](http://www.openx.org/publisher/enterprise-ad-server) - an open source alternative with varying price schemes (free begin an option for the tech savvy).  OpenX actually spawned [Piwik](http://piwik.org/), with the intent of offering an open source, extensible alternative to Google Analytics.  

What?  An open source, extensible alternative to Google Analytics?  You have my attention.

I am very interested in [Piwik](http://piwik.org/), and version 1.0 released near the end of August 2010\.  Already there are translations for a lot of non-English languages, and the plugin library is growing at a pleasant rate.  This details my installation experience and adding the service to Habari (powering this blog).

I was a little surprised that the Piwik installation instructions suggest to extract the archive locally, then FTP the contents to your server.  I suppose this better accommodates the myriad terrible hosting providers that do not elegantly support extract/compress on their servers.  Biting my tongue, I extracted locally and uploaded via FTP.  Loading the sub-directory from my server, I saw the initial screen, and moved right to the "Next" button.  Everything looks fine, except there are some issues in the "Optional - File Integrity" section.  I know it is in the optional section, but come on - File Integrity?  I feel like this should be addressed.  The message is: "File integrity check failed and reported some errors. This is most likely due to a partial or failed upload of some of the Piwik files. You should reupload all the Piwik files in BINARY mode and refresh this page until it shows no error."  The details look like:

![20101017_piwik_error.png](/files/20101017_piwik_error.png)

As it suggests, I flipped my FTP transfer to binary mode, replaced the three files in question, reloaded the index.php file to restart the installation process, and no more file integrity issues.

During the database configuration, I was pleased that PDO_mysql is the default, but for those individuals who do not have access to pdo, they offer mysqli as an option.  Great touch [Piwik](http://piwik.org/).  Create your database, provide the installer with the credentials, and, "Tables created successfully!".  

I added my initial site, pasted the provided JavaScript into my Habari footer.php (in HABARI_ROOT/user/themes/MY_THEME/footer.php), and  continued to the end of the installation procedure.  After pinging my site a half dozen times, I loaded the piwik site, and entered my user credentials.  UPDATE:  As Andy C points out in the comments, there is a [piwik plugin](https://trac.habariproject.org/habari-extras/browser/plugins/piwik/trunk) available for Habari.  Use this, as it keeps you from needing to update .../user/themes/NEW_THEME/footer.php (or whichever file you edited) each time you change themes.

The first load took a LONG time (minutes).  But apparently some caching or optimization is happening, because successive loads only required seconds to generate the same set of widgets.  I'm not including demonstrative Piwik screens, because mine are nearly empty, but I will link to the live demo that [Piwik offers here](http://demo.piwik.org/index.php?module=CoreHome&action=index&idSite=1&period=week&date=today#module=Dashboard&action=embeddedIndex&idSite=1&period=week&date=today).  It has a lot of data, and better displays the capabilities of the default widget set.

Overall, my initial impressions are positive.  A minor hiccup in the installation is not a concern, and I look forward to customization and optimization exercises.  I also like the fact that I have very direct access to the raw logs, and can generate custom plugins for the Piwik interface should I so choose.  

<span class="signature">
// TODO Habari theme(s), GWT deployment w/ App Engine 
// -- imperialWicket
</span>
