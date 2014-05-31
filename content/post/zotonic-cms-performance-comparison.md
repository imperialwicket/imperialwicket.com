{
  "title": "Zotonic CMS performance comparison",
  "description": "Zotonic CMS performance comparison",
  "date": "2011-08-24",
  "url": "zotonic-cms-performance-comparison/",
  "type": "post",
  "tags": [
    "erlang",
    "zotonic"
  ]
}
Zotonic (the Erlang CMS) claims to be "[Typically 10 times (and much more) faster than PHP content management systems](http://zotonic.com/features)". I am going to use [Siege](http://www.joedog.org/index/siege-home) to do some basic performance benchmarking against the demo installations of Zotonic, WordPress, Drupal, Joomla, and Habari. The performance comparison is an interesting claim for Zotonic, particularly because one of Zotonic's primary features is "Speed", as listed on their Features page, available from the site index.  A quick scan of [WordPress](http://wordpress.org/about/), [Drupal](http://drupal.org/about), [Joomla](http://www.joomla.org/about-joomla.html), and [Habari](http://wiki.habariproject.org/en/Manual) about pages show that these PHP content management systems do not put much focus in performance (no "performance" or "speed" references on any of those pages). I am sure that the development teams are attentive to major performance bottle necks, and that page loading speeds is important to these projects, but in the context of Zotonic's claim, none of these PHP CMS options claims speed/performance as a fundamental feature.  

To test performance in a reasonably fair way, I intend to do the most basic installation of each CMS that is possible. I will walk through the standard installation and use wizards or automated configurations and as soon as I get a site with demo data, I am going to see how well it performs using a standard siege report. I realize that there are a number of variables here that are ignored, but at a high-level, I think this is a good indication of the content management system's attention to performance. For example, the skeleton blog that Zotonic generates has about 6 pages, as compared to the demo Joomla site which has (I didn't actually check...) seemingly hundreds. Related, these various content management systems have vast distinctions in the integrated functionality. A more rigorous performance test would determine the CMS with the most functionality, and then add extensions, modules, etc. to the others such that the functionality field was as consistent as possible. While I am certain this would affect site performance, I do not feel that it will have grand impact on the final results. To reiterate, I also feel that the development teams for these projects inherently offer a valid performance representation in their demo content (independent of feature-set distinctions). 

A final pair of inconsistencies I will mention are the database server and web server distinctions.  The PHP content management systems all use MySQL by default, where Zotonic uses PostgreSQL. Both of these work well, and I am not getting into a PostgreSQL vs MySQL debate here. There is a lot to be said regarding performance in each of these database servers, but I am again claiming that the CMS devs indicate their performance focus by choosing the tools for their standard demo installation. For reference I am running PostgreSQL 8.4 and MySQL 5.1 (both installed via yum and using default configurations). I am testing the PHP content management systems on Apache 2.2, and I am not configuring any substantial performance updates

As an aside, I will also point out the download sizes after extraction (and give a big win to Habari...):

*   34MB Joomla 1.7.0
*   27MB Zotonic 0.7.1 (source)
*   14MB Drupal 7.7
*   13MB WordPress 3.2.1
*   5.2MB Habari 0.7.1

I am not going to make these installations publicly available, since it is straight-forward to install each of these on most systems, and I do not intend to make any major changes to the default installations. I think it would be interesting to design a relatively simple site, build it in each CMS, and then run the same benchmarks, but that is a much more grandiose endeavor. For reference, I am running Fedora 14 on an i7 i960 Intel processor with 24 GB of RAM and a solid state hard drive (more [system details](http://imperialwicket.com/searching-for-a-linux-machine-system76-leopard-extreme)).

Using this format:
<pre>
siege -c20 localhost/app -b -t30s
</pre>

<table>
<tr><th>CMS</th><th>Transactions</th><th>Data Transfer</th><th>Response time</th><th>Transaction rate</th><th>Longest Transaction</th></tr>
<tr><td>Joomla 1.7.0</td><td>2848</td><td>23.16MB</td><td>.20s</td><td>97/s</td><td>1.2s</td></tr>
<tr><td>Drupal 7.7</td><td>3426</td><td>13.09MB</td><td>.17s</td><td>117/s</td><td>.82s</td></tr>
<tr><td>WordPress 3.2.1</td><td>3284</td><td>10.01MB</td><td>.18s</td><td>112/s</td><td>.55s</td></tr>
<tr><td>Habari 0.7.1</td><td>6216</td><td>11.83MB</td><td>.10s</td><td>207/s</td><td>.28</td></tr>
<tr><td>Zotonic 0.7.1</td><td>20348</td><td>121.56MB</td><td>.03s</td><td>682/s</td><td>.70</td></tr>
</table>

Wow, it is not a 10x distinction, but impressive nonetheless.  Also, I did not put any performance optimizations in place. One concern of mine is immediately: How much of that was apache? I did a quick check with ajp, but that throttle Zotonic all the way back to 1200 transactions over 30 seconds, and it was clearly getting throttled by AJP. Since I doubt that anyone will be fronting Zotonic with Apache, I am going to avoid fine-tuning AJP to allow higher throughput for Zotonic.  

In the end, I want to highlight that the web server is definitely a limiting factor, but I will again echo that these are the standard web servers for each project.  As such, even the most basic install of each of these content management systems yields results that strongly favor Zotonic from a performance perspective.
