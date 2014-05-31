{
  "title": "Increase PHP memory_limit on GoDaddy shared Linux hosting",
  "description": "Increase PHP memory_limit on GoDaddy shared Linux hosting",
  "date": "2012-02-09",
  "url": "increase-php-memory_limit-on-godaddy-shared-linux-hosting/",
  "type": "post",
  "tags": [
    "apache",
    "linux",
    "dev"
  ]
}
I have to look this up everytime, and for some reason it is really difficult to find a good resource.  So after wasting another hour of my life searching random forums, here is what worked for me.

Using an FTP connection (it worked for me through a local FTP client or through the godaddy Hosting Control Center -> Content -> FTP File Manager), edit the php5.ini in your html directory. The full path is something like '/home/content/12/34567890/html/php5.ini' with different numerical values. Open that file. GoDaddy creates it for you when the account is created, if php5.ini is missing, someone probably deleted it, and you just need to make a new one.

In the php5.ini file, add the line:
<pre>
memory_limit = 128M
</pre>

The default is 64MB, which is enough for a lot of sites. Usually this needs to increase for the purpose of using particular extensions/plugins/modules in a CMS. Often image-handling extensions strongly encourage or even require 96M or 128M. I have not tested to see what the max is, but 128M is a good first step when 64M is no longer cutting it. 

Now add a php file to that same 'html' directory. We need to use this for executing phpinfo(), and validating the change. Create a 'temp.php' file and add the line:

<pre>
&lt;?php phpinfo(); ?&gt;
</pre>

If you load your new temp.php page in a browser (yourPrimaryDomain.com/temp.php), you should see the PHP version you are using and all the PHP info details. A couple of these are of primary interest:

*   Loaded Configuration File
*   memory_limit

Unless you got really lucky, "Loaded Configuration File" is probably "(none)" at this point. If so, then your memory_limit value is still "64M". The problem is that your server has a fastcgi process running, and hence the php5.ini contents were loaded before you made the change. In order to reload the new php5.ini file, you need to kill off the existent web processes. Go to your hosting control center, then Content -> System Processes, now use the 'End Web' button to kill the running processes. This does not impede your site loading, it just forces the server to spawn a new process for the next request. 

If you refresh the phpinfo page, you should see updated values for Loaded Configuration File and memory_limit. The server should have loaded your php5.ini file, and the memory_limit now reflects the updated value.

A couple other notes: 

*   Be sure you edit the php5.ini file in your root 'html' directory.  If you have subdomains or addon domains with their own directories, you still want to edit the file in your root 'html' directory.
*   Lots of places have data about php.ini affecting PHP 4 and php5.ini affecting PHP 5\. One would think that since GoDaddy is no longer offering PHP 4, you could use either php.ini or php5.ini. Nope. Stick to php5.ini.
*   Don't try to use the php_value_memory_limit directive in your .htaccess file, this causes the server to throw errors.
*   I see lots of accounts that indicate you must use GoDaddy's FTP File Manager to edit the php5.ini file in order for it to work properly. I can't confirm this, and I can only assume that this direction was coincidentally related to running processes for the users that reported the behavior.

Now delete the phpinfo() file; I called it 'temp.php'. You do not want to leave these laying about your server. While I'm offering suggestions, you should also consider transferring to another hosting provider.
