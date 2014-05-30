{
  "title": "Using the Google Distance Matrix Service",
  "description": "Using the Google Distance Matrix Service",
  "date": "2011-06-21",
  "url": "using-the-google-distance-matrix-service",
  "type": "post",
  "tags": [
    "google maps api"
  ]
}
Distance Matrix seems to have limited use for most, and a few niche markets that could really take advantage of the service.  The problems I encountered using the service are that samples and documentation are minimal in comparison to other Google services, and the request limitations are quite restrictive (100 results per 10 seconds with a 2500 daily maximum for non-Premier requests).  I will note here also that there is a hard limit of 25 origin points, and 25 destination points.  So if you have 100 origins all going to a single destination, even though the request falls within 100 results, this requires four requests.  To combat these problems, I want to describe one way to lessen the impact of the request limitations, and offer some details about how I use the Distance Matrix service. 

#### Distance Matrix Basics

The most basic Google Distance Matrix service request takes an array of origins and an array of destinations, and returns a JSON or XML set of distances and times.  The base url is:

<pre>
http://maps.googleapis.com/maps/api/distancematrix/json?
</pre>

Append pipe delimited (URL-encoded) series of origins and destinations, and add the Maps API Family "sensor=false" to make an actual request.  If you use Chrome/Chromium or have the appropriate Firefox extension, you can load the JSON in your browser with the full URL:

<pre>
http://maps.googleapis.com/maps/api/distancematrix/json?origins=Vancouver+BC|Seattle&destinations=San+Francisco|Victoria+BC&sensor=false
</pre>

I am going to use JSON in the display, but if you prefer xml, just switch the "json?" flag at the end of the base url to "xml?", and you will see all those lovely tags.

You should see JSON amounting to:

<pre>
{
   "destination_addresses" : [ "San Francisco, CA, USA", "Victoria, BC, Canada" ],
   "origin_addresses" : [ "Vancouver, BC, Canada", "Seattle, WA, USA" ],
   "rows" : [
      {
         "elements" : [
            {
               "distance" : {
                  "text" : "1,526 km",
                  "value" : 1526236
               },
               "duration" : {
                  "text" : "15 hours 50 mins",
                  "value" : 56997
               },
               "status" : "OK"
            },
            {
               "distance" : {
                  "text" : "109 km",
                  "value" : 109313
               },
               "duration" : {
                  "text" : "3 hours 16 mins",
                  "value" : 11735
               },
               "status" : "OK"
            }
         ]
      },
      {
         "elements" : [
            {
               "distance" : {
                  "text" : "1,300 km",
                  "value" : 1300026
               },
               "duration" : {
                  "text" : "13 hours 24 mins",
                  "value" : 48224
               },
               "status" : "OK"
            },
            {
               "distance" : {
                  "text" : "296 km",
                  "value" : 296219
               },
               "duration" : {
                  "text" : "5 hours 10 mins",
                  "value" : 18594
               },
               "status" : "OK"
            }
         ]
      }
   ],
   "status" : "OK"
}
</pre>

The JSON returns you the destinations and origins that you provided, along with the row elements to fill out the matrix.  Destinations make up your column headings, and origins label your rows.  Each "row" "element" represents a table cell in the resulting matrix.  Notice that the distance is in kilometers, while this is great for some, others may want imperial.  There are a few other helpful parameters you can pass in your request to shape your output, I will offer the defaults first with emphasis, where a default parameter is used:

*   mode: _driving_, walking, bicycling
*   language: I think this defaults based on the IP of the request, but have not confirmed this exhaustively.  Use [this spreadsheet](https://spreadsheets.google.com/pub?key=p9pdwsai2hDMsLkXsoM05KQ&gid=1) as a reference for options
*   avoid: tolls, highway
*   units: _metric_, imperial
*   sensor: true, false

#### Displaying data and the Google example

As far as I can tell, the only Google sample is [here](http://gmaps-samples-v3.googlecode.com/svn/trunk/distancematrix/london.html).  It is a pretty slick treatment with hover listeners and a dynamically changing map based on selection/hover over the matrix.  I can see this being a helpful technique for businesses or events that need to map there matrix.  However, for my purposes, the map was superfluous, as I simply wanted distance/time data for my origins and destinations.  Also, I am only concerned with Driving times, so I can do away with the selection options for mode.

#### A simplified sample with attention to query limitations

To make things a little easier, I am using the Google sample as a baseline.  The sample from Google uses their maps API v3, which wraps the requests in a google.maps.DistanceMatrixService() object.  I put together a basic update to their code that includes the following modifications:

*   Map is removed
*   Driving/Walking option is removed
*   Matrix requests are automatically limited to requests under 100
*   Destinations and Origins can be pasted into the page as pipe-delimited values
*   Large requests require a user to wait 10 seconds then select the "Add more values" button
*   A clear option is provided to reset the matrix

A full index.html page is available on [GitHub](https://github.com/imperialwicket/googleDistanceMatrixSample/blob/master/index.html).  I am not making any explicit copyright on this, although I would probably lean toward [CC-BY](http://creativecommons.org/licenses/by/3.0/).  Note that this is intended to be a small usage, local utility, or a learning mechanism.  I would advise against putting this on a server for any substantial amount of usage (unless you have a premier account, in which case the limits can be made much higher anyway).  Feel free to post questions, code recommendations, etc. in the comments.  
