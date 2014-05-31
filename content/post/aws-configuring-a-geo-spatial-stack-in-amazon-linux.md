{
  "title": "AWS:  Configuring a Geo-spatial stack in Amazon Linux",
  "description": "AWS:  Configuring a Geo-spatial stack in Amazon Linux",
  "date": "2010-12-26",
  "url": "aws-configuring-a-geo-spatial-stack-in-amazon-linux/",
  "type": "post",
  "tags": [
    "aws",
    "geoserver",
    "postgresql",
    "postgis",
    "apache",
    "tomcat"
  ]
}
My usual geo-spatial stack includes CentOS, PostgreSQL, PostGIS, Apache HTTP Server, Apache Tomcat, GeoServer, and a front-end that uses OpenLayers, possibly with GeoExt and/or Google Web Toolkit.  While this is not terribly complex to configure, it can be a daunting for task for individuals looking to get started.  There are few good resources for getting parts of this configuration together, but I have not found a good collection from the ground up, so I thought I would give it a go.  I hardcoded a lot of filenames, you should usually check the download directory to be sure you are grabbing the latest stable packages. 

I am starting with a basic Amazon Linux EC2 instance (EBS backed).  If you need help getting an instance up and running, check my earlier post on [basic LAMP instances in AWS](http://www.imperialwicket.com/aws-building-a-lamp-instance).  The earlier post details the installation of Apache (HTTP Server), MySQL, and PHP.  All of these are fine to have on your server, but we will not use MySQL or PHP for our Geo-spatial stack (although you may want them for supporting a web front-end).  Note that the configuration we are building is fine for testing or VERY light-duty servers.  It is NOT suitable for live services or large data sets.  After some additional testing I may post instructions or just an AMI for geospatial, we'll see.  One final note, this should work with minimal changes on CentOS, Fedora, Red Hat, etc.  Debian/Ubuntu - you will need to replace yum with apt-get, change the "...-devel" packages to "...-dev" (in most cases), make small changes to a number of your paths (liberal use of 'sudo updatedb' and 'locate <filename>' should work), and note that 'httpd' becomes 'apache2'.  If you are installing on anything other than Amazon Linux, you should consider installing the Sun JDK and mod_jk from your OS repos - probably extras/testing/dev/universe/whatever, it will be easier to track that way.  I would not recommend installing PostGIS from a repository, even though some have it available.  I'm sure they work just fine, but they tend to be older releases, and library/dependency issues always come up.  Besides, you should build things from source from time to time - it's like eating your GNU/Linux veggies.   

OK, once your EC2 instance is up, and you are connected via ssh, we can start our installations.

Run yum update - we need this to get some of the build tools, and it's always good practice when you're building out the server to start with current packages.
<pre>
yum update
</pre>

Install postgresql, postgresql-devel, and postgresql-libs.  Configure your data directory and start the service (as the postgres user).
<pre>
sudo yum install postgresql postgresql-server postgresql-devel
sudo mkdir /usr/local/pgsql/
sudo mkdir /usr/local/pgsql/data
sudo chown postgres /usr/local/pgsql/data
sudo su postgres
initdb -D /usr/local/pgsql/data
postgres -D /usr/local/pgsql/data &
exit
</pre>

Install PostGIS.  This requires some build tools, and also the GEOS and PROJ libraries.  On a Micro instance, making the GEOS and PROJ libraries will take a while (about 5-10 minutes), and drive your server load average to the 1.00-2.00 range.  I'm going to unnecessarily change directories to what should be the current working directory a few times, just to make sure we're in the same place.  After GEOS, PROJ, and PostGIS are built, we will also need to update our libraries, so the server knows where to find them.

<pre>
sudo yum install gcc make gcc-c++ libtool libxml2-devel
# make a directory for building
cd /home/ec2-user/
mkdir postgis
cd postgis

# download, configure, make, install geos
wget http://download.osgeo.org/geos/geos-3.2.2.tar.bz2
tar xjf geos-3.2.2.tar.bz2
cd geos-3.2.2
./configure
make
sudo make install

# download, configure, make, install proj
cd /home/ec2-user/postgis/
wget http://download.osgeo.org/proj/proj-4.7.0.tar.gz
wget http://download.osgeo.org/proj/proj-datumgrid-1.5.zip
tar xzf proj-4.7.0.tar.gz
cd proj-4.7.0/nad
unzip ../../proj-datumgrid-1.5.zip
cd ..
./configure  
make
sudo make install

# download, configure, make, install postgis
cd /home/ec2-user/postgis/
wget http://postgis.refractions.net/download/postgis-1.5.2.tar.gz
tar xzf postgis-1.5.2.tar.gz 
cd postgis-1.5.2
./configure --with-geosconfig=/usr/local/bin/geos-config
make
sudo make install

# update your libraries
sudo su
echo /usr/local/lib >> /etc/ld.so.conf
exit
sudo ldconfig
</pre>

Now that PostGIS is installed, we should create a template database for PostGIS.  Anytime you are generating a new database that requires geospatial data, you can create it from this template. 
<pre>
createdb -U postgres template_postgis
createlang -U postgres plpgsql template_postgis
psql -U postgres -d template_postgis < /usr/share/pgsql/contrib/postgis-1.5/postgis.sql
psql -U postgres -d template_postgis < /usr/share/pgsql/contrib/postgis-1.5/spatial_ref_sys.sql
</pre>

Install the Sun JDK, I'll note again that if you are using a non Amazon Linux, this is probably available from a repo.  I didn't try, but as I'm reviewing this process, it occurs to me that you could likely install the sun-java6-jdk from the CentOS repositories, because I don't think it has any external dependencies.  I'll check it next time through.  For now: 
Go here:  [Oracle Java downloads](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (I think I actually experienced physical pain putting the words "Go here" and "Oracle" in the same sentence.  UPDATE:  David Winslow (GeoServer Core Committer) noted in the comments that the current stable build runs fine on recent OpenJDK releases.
Select the "Download JDK" button.
Choose Linux x64, agree to the License agreement, and click continue.
Download the ...rpm.bin file.
SCP that file over to the /home/ec2-user/, then:
<pre>
cd /home/ec2-user
sudo chmod 665 jdk-6u23-linux-x64-rpm.bin
sudo ./jdk-6u23-linux-x64-rpm.bin
# [Enter]
sudo ln -sf /usr/java/jdk1.6.0_23/bin/java /usr/bin/java
</pre>

Install apache HTTP Server and Apache Tomcat
<pre>
sudo yum install httpd httpd-devel tomcat6
</pre>

Download the GeoServer web archive and move it to Tomcat's webapps directory
<pre>
cd /home/ec2-user/
wget http://sourceforge.net/projects/geoserver/files/GeoServer/2.0.2/geoserver-2.0.2-war.zip/download
unzip geoserver-2.0.2-war.zip
sudo chown tomcat:tomcat geoserver.war
sudo mv geoserver.war /var/lib/tomcat6/webapps/
</pre>

Download, configure, make, and install mod_jk (Apache module for connecting apache web server to tomcat).  This allows you to access web applications in tomcat through Apache HTTP Server over port 80\.  If you skip this, be sure that your security group allows access over the port on which tomcat is listening (default is 8080).
<pre>
cd /home/ec2-user
mkdir mod_jk
cd mod_jk
wget http://www.devlib.org/apache//tomcat/tomcat-connectors/jk/source/jk-1.2.31/tomcat-connectors-1.2.31-src.tar.gz
tar xzf tomcat-connectors-1.2.31-src.tar.gz
cd tomcat-connectors-1.2.31-src/native
./configure --with-apxs=/usr/sbin/apxs
make
sudo make install
</pre>

Once mod_jk is installed, we need to update the Apache HTTP Server config and create a workers.properties file so that Apache HTTP Server and Apache Tomcat can communicate via mod_jk.  This is a mediocre configuration; it is barely more than the minimum required (as documented by Apache [here](http://tomcat.apache.org/connectors-doc/generic_howto/quick.html)).
<pre>
sudo vim /etc/httpd/conf/httpd.conf

ADD TO BOTTOM:
 # Load mod_jk module
 # Update this path to match your modules location
 LoadModule    jk_module  /usr/lib64/httpd/modules/mod_jk.so
 # Where to find workers.properties
 # Update this path to match your conf directory location (put workers.properties next to httpd.conf)
 JkWorkersFile /etc/httpd/conf/workers.properties
 # Where to put jk shared memory
 # Update this path to match your local state directory or logs directory
 JkShmFile     /var/log/httpd/mod_jk.shm
 # Where to put jk logs
 # Update this path to match your logs directory location (put mod_jk.log next to access_log)
 JkLogFile     /var/log/httpd/mod_jk.log
 # Set the jk log level [debug/error/info]
 JkLogLevel    info
 # Select the timestamp log format
 JkLogStampFormat "[%a %b %d %H:%M:%S %Y] "
 # Send everything for context /geoserver and /geoserver/* to worker named geoserver_worker (ajp13)
 JkMount  /geoserver/* geoserver_worker
 JkMount  /geoserver   geoserver_worker
END ADD

sudo vim /etc/httpd/conf/workers.properties

ADD TO FILE:
 # Define the list of workers that will be used
 worker.list=geoserver_worker
 # Define geoserver_worker
 worker.geoserver_worker.port=8009
 worker.geoserver_worker.host=localhost
 worker.geoserver_worker.type=ajp13	
END ADD
</pre>

Almost there.  Start Apache HTTP Server and Apache tomcat.
<pre>
sudo /sbin/service httpd start
sudo /sbin/service tomcat6 start
</pre>

On a micro instance, it takes a minute or two for the GeoServer to initialize.  Once it's ready, navigate in a browser to "[your ec2 ip]/geoserver" and login with the default credentials (admin/geoserver).  GeoServer comes with a number of demonstrative layers, and at the bottom of the left menu, you can use "Layer Preview" to investigate some of the pre-packaged layers.  Use the OpenLayers format, and don't forget to click on the map to request featureInfo for that location.  Note that there is a bug in the 2.0.2 release that causes problems for some of the shapefiles, the following should be working:  tiger:tiger_roads, nurc:Img_Sample, topp:tasmania_water_bodies, and topp:tasmania_state_boundaries.  I usually delete all of the pre-loaded data to keep my GeoServer clean, but they can be a handy reference if you are just getting started.  The OpenLayers preview page is a great page to get started with the html/javascript necessary to get your geospatial data on a web page. 

Let's do one more thing.  Since we went through the trouble of installing PostgreSQL and PostGIS, we should put some geospatial data in the database and display that too.  Our steps will be to create a new PostgreSQL database (using the template_postgis database we created after the PostGIS installation), create a table in that database using a shapefile (shp2pgsql), and create a table manually using SQL.  Once the tables are created, we'll load them into GeoServer and review our data.

For the sake of simplicity, we will use on of the preloaded shapefiles that comes with GeoServer.  Since the tiger:poly_landmarks shapefile does not load correctly in GeoServer 2.0.2 (at least not for me), let's try injecting that shapefile into the database, and loading the data from there.  The '-s' flag is for spatial projection, I'm not going to get into spatial projection here, other than to say it is important.  Go to [spatialreference.org](http://spatialreference.org/) for more info, and useful details regarding spatial projections.  If you want to use google maps as a background, you may also be interested in EPSG:900913 (http://spatialreference.org/ref/sr-org/6627/), the PostGIS spatial_ref_sys INSERT statement will be handy.  The '-I' instructs shp2pgsql to apply an index (of type GiST) to the geometry column.  This won't be very noticeable for this particular data set, but on larger data sets it makes a huge difference.

Create a table form a shapefile, using shp2pgsql.  For those not familiar, I will point out that all shp2pgsql does is create a file with sql insert statements that regenerate the shapefile in your database.  The usual flow is to use shp2pgsql to generate the inserts in a *sql file, then inject that sql file in your PostGIS enabled database.
<pre>
cd /home/ec2-user/
createdb  -U postgres -T template_postgis postgis_test
shp2pgsql -s 4326 -I /var/lib/tomcat6/webapps/geoserver/data/data/nyc/poly_landmarks.shp public.poly_landmarks > poly_landmarks.sql
psql -U postgres -d postgis_test < poly_landmarks.sql 
</pre>

You can also manually create geospatial data using PostGIS functions.  For me, this is often useful fo pre-existing lists of lat/lon values, but there are many possibilities.
<pre>
psql -U postgres postgis_test
postgis_test=# CREATE TABLE some_points (id SERIAL, name VARCHAR(24), description VARCHAR(64), some_num INTEGER, lat NUMERIC(9,6), lon NUMERIC(9,6));
postgis_test=# SELECT AddGeometryColumn('public','some_points','the_geom',4326,'POINT',2);
postgis_test=# INSERT INTO some_points (name,description,some_num,lat,lon) VALUES 
('point a','the point at a',15,40.7879,-73.9567),
('point b','the point at b',14,41.1234,-73.7689),
('point c','the point at c',22,40.5645,-73.1145),
('point d','the point at d',54,40.9844,-73.1212),
('point e','the point at e',21,39.9889,-72.9554),
('point f','the point at f',93,41.2543,-73.6555);
postgis_test=# UPDATE some_points SET the_geom = ST_GeomFromText('POINT('||lat||' '||lon||')',4326);
postgis_test=# ALTER USER postgres WITH PASSWORD 'password';
</pre>

Back to GeoServer.  There is a great walk-through for adding PostGIS data to GeoServer in the [GeoServer docs](http://docs.geoserver.org/stable/en/user/gettingstarted/postgis-quickstart/index.html), I'll offer an abridged cut (There are some helpful screenshots in the docs, if you like those).  

1.  On the left menu, select Workspaces, then at the top, "Add new workspace".
2.  Provide a Name ("postgis_test") and a Namespace URI ("postgis_test"), and submit.
3.  Back on the left menu, select "Stores" then at the top, "Add new store", then choose PostGIS.
4.  Choose the new "postgis_test" workspace, and provide a Data Source Name ("postgis_testDB").
5.  Use connection parameters host: localhost, port: 5432, database: postgis_test, schema: public, user: postgres, passwd: password (Password is whatever you set with the ALTER USER command), and select "Save".

After the save completes, you see the New layer chooser screen, which should have your poly_landmarks and some_points tables listed.  Use the "Publish" option to publish poly_landmarks.  The defaults are fine for our purposes, just be sure to select the "Compute from data" option and then the "Compute from native bounds" option under the Bounding Boxes section, then select "Save".  Now you can preview the poly_landmarks layer, just be sure to select the layer from the postgis_test workspace.  Follow the same process to publish the some_points layer.

I'll follow up with some basics for styling layers and displaying multiple layers using OpenLayers soon.

Further Reading:

[PostgreSQL 8.4 docs](http://www.postgresql.org/docs/8.4/interactive/index.html)

[PostGIS 1.5 docs](http://postgis.refractions.net/documentation/)

[GeoServer 2.0 docs](http://docs.geoserver.org/stable/en/user/)

[SpatialReference.org](http://spatialreference.org/)

[OpenLayers](http://openlayers.org/)

[Apache HTTP Server](http://httpd.apache.org/docs/2.0/)

[Apache Tomcat](http://tomcat.apache.org/tomcat-6.0-doc/index.html)

[Apache Tomcat Connectors](http://tomcat.apache.org/connectors-doc/generic_howto/quick.html)
