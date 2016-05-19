{
  "title": "Opensource CSV to shapefile conversion (PostGIS)",
  "description": "Opensource CSV to shapefile conversion (PostGIS)",
  "date": "2011-05-20",
  "url": "opensource-csv-to-shapefile-conversion/",
  "type": "post",
  "tags": [
    "opensource",
    "postgis",
    "gis"
  ]
}
My primary tool for GIS manipulation and review on the desktop is the opensource [uDig](http://udig.refractions.net).  This is because most of my GIS data is already created, and is available in either [PostGIS](http://www.postgis.org/) or [GeoServer](http://geoserver.org/).  uDig interfaces well with each of these.  When the occasional shapefile comes my way, it is as easy as dragging and dropping the file into the uDig pane, and I am in business.  uDig is based on Eclipse, and uses a similar display/window arrangement.  If you are not comfortable with Eclipse, or want to try something a little different - [Quantum GIS](http://qgis.org/) (with [GRASS GIS](http://grass.osgeo.org/)) is also a great choice.  These tools are all opensource and have active communities.

One area where both of these tools fall a little short, is generating shapefiles given a series of points.  There are a handful of scripts that are mentioned in forums and mailing lists, and an occasional plugin for uDig and/or Quantum GIS, but nothing that really makes it simple and reliable.  

I can not promise to make it simple, but when creating shapefiles programmatically, PostgreSQL with PostGIS is a great utility.  This is particularly true when you have a multitude of latitude and longitude values in a csv file, which can be imported into PostgreSQL without much difficulty.  This also eases the burden of manually adding attributes, or generating some type of script to automate the attribute table generation - since the attributes can be added directly to the PostGIS table.  

Assuming you have PostgreSQL/PostGIS and (optionally) either uDig or Quantum GIS installed, the basic steps are:

1.  Generate a new table with appropriate columns to match your csv data.
2.  Populate the new table using your csv data.
3.  Use the PostGIS addgeometrycolumn function to add a geometry column to the new table.
4.  Use the PostGIS geometryfromtext function to populate the geometry column using your latitude/longitude values
5.  Use the pgsql2shp utility that ships with PostGIS to create a shapefile based upon your new table

    OR

    Open the PostGIS layer in uDig/Quantum GIS and export the layer to shapefile.

#### Generate a new table

My test data is pretty simple; it includes columns for latitude, longitude, name, and value.  I am not sure why, but my data has ridiculously extreme precision, as you can see by my NUMERIC columns.  

<pre>
CREATE TABLE public.my_data (
    id SERIAL PRIMARY KEY,
    latitude NUMERIC(12,8),
    longitude NUMERIC(12,8),
    value NUMERIC(12,8),
    name VARCHAR(64)
    );
</pre>

NOTE:  I wanted to keep this simple and generic for the purpose of demonstration, but I want to point out that using columns with names: "name" and "value" is a terrible idea.

#### Populate the table using your csv data

Use PostgreSQL to COPY the data from CSV to your new table.  A couple things to check here: permissions are important if you are on *nix, PostgreSQL is particular about running as a non-root user, make sure the postgres user can access your CSV file.  

If you happen to have an id value already, you can use it (the surrogate/natural primary key argument does not belong here).  If not, just be sure to use a SERIAL column as a primary key, and PostgreSQL will do the work for you.  While a primary key is not necessary, a lot of supporting applications require them.  Make sure that you explicitly list your columns, and that they correspond to the order in your csv file.  Finally, remember to remove your column headers from the csv file; there are ways around this, but I always find it easiest to just remove them.  

<pre>
COPY my_data (latitude,longitude,value,name)
FROM '/path/to/my_data.csv' CSV;
</pre>

#### Use addgeometrycolumn function on the new table

My data is simple latitude and longitude values, so spatial projection is pretty open.  I am going to use 4326, which is a pretty common WGS84 projection.  I need to add a geometry column to my table, and set its geometry type to POINT (2-dimensional) and spatial projection to 4326.

<pre>
SELECT addgeometrycolumn('public','my_data','the_geom',4326,'POINT',2);
</pre>

This adds an empty geometry column to the public.my_data table, with column name 'the_geom', and constraints allowing for only geometries of type POINT with two dimensions and in spatial projection 4326.

#### Use geometryfromtext function to populate the geometry column

This function always ends up a little cumbersome because of the string concatenation, but generating points from lat/lon values usually goes something like this:

<pre>
UPDATE public.my_data SET the_geom = ST_GeomFromText('POINT('||longitude||' '||latitude||')',4326);
</pre>
** Thanks to mstuyts for the updated function here! **

In this case, the geometryfromtext(text) function would work well, but I usually default to the geometryfromtext(text,spatial-projection) version of the function.  It never hurts to be explicit.

#### Export a shapefile

For some, this is not a necessary step, as the primary goal is to be able to add your csv points in a manner that allows you to investigate the points in the context of other GIS layers.  However, if you want to be able to pass the resulting data easily from one person to the next, a shapefile can be a convenient means of transfer (if you have bandwidth, [MapServer](http://www.mapserver.org/) or [GeoServer](http://geoserver.org/) are also a great avenue, and they can interface directly with PostGIS and PostgreSQL).  

Once you have the spatially projected data, it becomes a lot easier to generate a shapefile.  You can import the PostGIS layer to uDig or Quantum GIS, and save the layer as a shapefile.  In uDig, this is accomplished with  File -> Export -> Layer to Shapefile; and in Quantum GIS it is just as easy to use Layer -> Save As (selecting "ESRI Shapefile").  

Since we are working with the PostGIS tools, I will also highlight the command line option PostGIS provides to output shapefiles from PostGIS tables.  Depending on how you installed PostGIS, pgsql2shp may or may not be available on your PATH.  It is _probably_ in /usr/lib/postgresql/#.#/bin/ - where #.# is the PostgreSQL version you are running.

<pre>
pgsql2shp -f my_data -h localhost -u postgres -P password theDatabaseName "SELECT * FROM public.my_data"
</pre>

There you have it.  CSV points to a shapefile.  There are several steps here, and it is not for the faint of heart, but we avoided any obscure downloads, legacy plugins, broken links, and proprietary software.  If you have other solutions for this problem, feel free to offer alternatives in the comments.
