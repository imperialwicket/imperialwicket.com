{
  "title": "USDA Farmers Markets - To API or not to API",
  "description": "USDA Farmers Markets - To API or not to API",
  "date": "2011-08-09",
  "url": "usda-farmers-markets-to-api-or-not-to-api/",
  "type": "post",
  "tags": [
    "google maps api"
  ]
}
#### Background

[Code For America: Farmers Market API](http://codeforamerica.org/2011/08/08/farmers-market-api/)

[Hacker News Commentary](http://news.ycombinator.com/item?id=2861458)

_Impatient? Want the JSON?  [It's over here](https://github.com/imperialwicket/usdaFarmersMarkets)._

#### My thoughts

At first, I thought this was great, and I started thinking about whipping together an Android application that used the API. After putting together a couple of screens and thinking about how little control I had over performance, I started to side strongly with cousin_it from the Hacker News commentary. How large is this data set really? And since any updates are going to need to manually push anyway, what is the benefit in offering the API?  

Don't read to far into this, I definitely think John's initial work was worthwhile, and it opened my eyes to a useful data set I had not previously endeavored to find. Nonetheless, from a performance a practicallity standpoint (for this data set) - I think a simple JSON object is probably more justified. 

Also, the map performs terribly, I knew from some experience with the MarkerClusterer (I hadn't touched it since back in the API V2 days) that the performance could be drastically improved with clustering. This was also reaffirmed in the HN Comments (noblethrasher).

#### Stepping back

Ok, scratch the Android app, what about a map? Easy enough, but without the API, I need the data. I must admit, I admire John's capabilities even more here. It didn't cost me any money, but it sucked away far more than an hour of my life. 

First, the Excel format download from USDA would not actually load in LibreOffice. I even tried firing up an old Mac that had Office 2008 on it. After 15 minutes of office updates and installation procedures on the old Mac (I apparently had never used Office on it), I found that the downloaded Export.xls file crashed Excel 2008 Mac too. More determined than ever, I threw a handful of sed commands at the file until I had something that looked more like a csv file and less like my cat sitting on my keyboard repeatedly pasting:
<pre>
< /td>< td align='left' valign='top' style='word-wrap:break-word;'>
</pre>
 onto the screen. There are a lot of stray line breaks, a few special characters, and who knows what else in there.  

I also tried to load the original download into Google Refine before manually updating, no luck there either. On that note, I also need to thank John for highlighting [Google Refine](http://code.google.com/p/google-refine/) (and its origin Freebase Gridworks). I was not familiar with the project, and I think I will use it quite a bit.

Pretty smooth sailing once I had the Google Refine project loaded. I spent a little time cleaning (not enough), and set about making a rudimentary Google Map page that uses marker clustering. A careful eye will notice that the index.html is suspiciously similar to several of the default Google Maps sample pages.

#### The JSON and a sample Map

I did not want to host the json files myself or deal with the API key for the map, so I am just posting the files to [my github account](https://github.com/imperialwicket/usdaFarmersMarkets). If you pull the files they should run fine API key-less from a localhost environment. Anyone who wants to grab the JSON can then do whatever they want with the data, and not deal with some of the cleanup that I already performed.

The [index.html](https://github.com/imperialwicket/usdaFarmersMarkets/blob/master/index.html) file also includes a decent sample of getting the MarkerClusterer set up with InfoWindows too. The code is dirty, and could use some comments, but it is functional.  

People commented on both codeforamerica and hacker news that localharvest.org does something similar already, though I don't believe they offer the api. Also, their map does not perform all that much better. It is speedier to load, but it fails a little bit on the usability spectrum. Again, in case anyone missed [the JSON](https://github.com/imperialwicket/usdaFarmersMarkets/blob/master/usda_farmers_markets_full.json).
