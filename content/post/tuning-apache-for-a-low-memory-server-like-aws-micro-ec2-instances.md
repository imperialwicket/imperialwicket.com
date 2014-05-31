{
  "title": "Tuning Apache for a low memory server (like AWS Micro EC2 instances)",
  "description": "Tuning Apache for a low memory server (like AWS Micro EC2 instances)",
  "date": "2012-01-09",
  "url": "tuning-apache-for-a-low-memory-server-like-aws-micro-ec2-instances/",
  "type": "post",
  "tags": [
    "aws",
    "apache"
  ]
}
One of my goals for 2012 is moving away from shared hosting and toward cloud and/or small VPS servers. For now, I am targeting AWS Micro instances for a lot of my efforts. Ultimately, I will probably move to Lighttpd or Nginx, but in the mean time I thought I would relay a configuration that is working well for me on low traffic sites. 

For anyone seeing performance or stability issues on a micro instances (using LAMP) that get reasonable traffic, you are probably encountering something like:

*   WSOD (White screen of death) - most often this is actually a out of memory error in PHP
*   MySQL failing to start or getting killed - this is usually apache using all the memory and the kernel shutting down the MySQL server
*   Actual output of Out Of Memory errors in PHP - really the same as above
*   MySQL failing to start due to pthread create returned 11 - MySQL can't create a new thread, probably because apache is using too many.

The root cause of these problems is lack of memory. Before you shut down your micro instance in favor of a small instance, or go back to shared hosting, consider tuning apache down a few notches. The default apache configuration is actually quite inappropriate to a server with 1GB or less memory. The default changes a little from one Linux distribution to the next, but for reference I'll describe the Amazon Linux defaults.

### Amazon Linux default apache config

This is all stored in the /etc/httpd/conf/httpd.conf file. There is a lot in this file, but most of what we want to discuss is near the beginning. Our biggest concerns have to do with KeepAlives and the prefork module. If you are using workers, you will also want to review the worker module configuration -- which is very analogous to the prefork module configuration.

###### Timeout

The default timeout is 60 (seconds). This is ridiculous. Have you ever waited 60 seconds for a site to load? I turn this down to the 20-30 second range.

###### KeepAlive

KeepAlive defaults to Off in Amazon Linux. Enabling KeepAlive can avoid generating/killing so many apache child threads, but if your users tend to only view a page or two, this makes no difference, because apache only persists the thread for a given connection. If you have high user engagement, you probably want KeepAlive on, but you should tune it down to keep your total apache thread count low. I have it enabled in my example config below, but for many of my sites, visitor engagement is relatively low, and I simply leave it disabled.

###### MaxKeepAliveRequests and KeepAliveTimeout

If you enabled KeepAlive on your low memory server, you should reduce the MaxKeepAliveRequests and KeepAliveTimeout. MaxKeepAliveRequests defaults to 100, which seems relatively low, but how many users do you actually have making 100 requests to your server per session? Related to this is the KeepAliveTimeout value, which defaults to 15 (seconds). In my mind, 15 seconds is longer than a short page view requires, and shorter than a long page view requires. That is, if someone hits my site and realizes the page is not what they want, they look at tags/categories/menu/whatever and decide to look at something else. The time required for this is usually more like 5-10 seconds. There is no reason to keep the thread alive for 15 seconds. The opposite end of this spectrum is a user who reviews an entire page, and then elects to review a previous or alternate article, in this scenario more than 15 seconds often elapses, and the user generates a new thread anyway. If you enabled KeepAlive, I recommend reducing KeepAliveTimeout to 5 or 10 seconds.  MaxKeepAliveRequests probably won't be an issue, but 25 or 50 is likely to behave in the same way as 100, and under the low chance that something awkward is happening, the lower maximum will curtail the issue sooner.

###### Prefork Module

Prefork is where the real magic happens. This is where we can tell apache to only generate so many processes. The defaults here are high, limiting you to a max of 256 servers. Just to put this in perspective, let's say you get 10 concurrent requests for a php page, and that page happens to require around 64MB of RAM when requested (well within the default php memory_limit of 128MB on Amazon Linux). That's around 640MB of RAM usage on your 613MB micro instance. And that's with only 10 connections - apache is configured to allow 256! I scale these down a lot, usually sticking with 10-12 MaxClients. As we mentioned in our example, this is still a dangerous number because 10-12 concurrent connections would use all our memory. If you want to be really cautious, make sure that your max memory usage is less than 613MB (on an AWS micro instance - 512MB is common on VPS from other providers).  Something like 64M php memory limit and 8 max clients keeps you under your limit with space to spare - this helps ensure that the kernel won't do something crazy like killing the MySQL process when your server is under load. 

You can play with the MinSpareServers, MaxSpareServers, StartServers, and MaxRequestsPerChild; but within the context of less than 20 MaxClients, making changes like 3 StartServers vs 5 StartServers will have almost no visible impact.

Without further ado, here are some values to get you started.  Tweak away, and have fun - welcome to httpd.conf (use apache2.conf if you're on debian).

#### /etc/httpd/conf/httpd.conf

<pre>
Timeout 30
KeepAlive On
MaxKeepAliveRequests 50
KeepAliveTimeout 10

&lt;IfModule prefork.c&gt;
    StartServers          3
    MinSpareServers       2
    MaxSpareServers       5
    MaxClients            10
    MaxRequestsPerChild   1000
&lt;/IfModule&gt;

# if you are using workers, update the IfModule worker.c section similarly.
</pre>

#### /etc/php.ini

<pre>
memory_limit = 100M
</pre>
