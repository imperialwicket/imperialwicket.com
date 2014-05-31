{
  "title": "The Mob",
  "description": "The Mob",
  "date": "2012-09-04",
  "url": "the-mob/",
  "type": "post",
  "tags": [
    "aws",
    "erlang",
    "chicago boss",
    "the mob"
  ]
}
The Mob is an Amazon Machine Image that demos a group of [Chicago Boss](http://chicagoboss.org) applications showcasing basic functionality and database connectivity to PostgreSQL, MySQL, and MongoDB.

You can launch The Mob with ami-ef47f186, it is publicly available.

The applications are available on Github:

*   [Chicago Boss MongoDB App](https://github.com/imperialwicket/chicagoboss_mongodb_app)
*   [Chicago Boss MySQL App](https://github.com/imperialwicket/chicagoboss_mysql_app)
*   [Chicago Boss PostgreSQL App](https://github.com/imperialwicket/chicagoboss_postgresql_app)

The Mob is configured with MongoDB, MySQL, and PostgreSQL apps pulled from the Github repositories, so that you can easily update a running instance if the demo apps are updated. However, I recommend launching the instance, and using it as a virtual playground and experiment area in Chicago Boss, without any of the overhead of configuring an environment.

### The Mob details

The primary user is 'boss'. You can connect to a newly launched instance with:
<pre>
ssh -i /path/to/key.pem boss@[IP or public DNS]
</pre>

The Mob comes with:

*   Erlang R15B01 - built from source w/o wxWidgets
*   MongoDB 2.2.0 - installed from 10gen repo
*   MySQL 5.5.24 - installed from amzn repo
*   PostgreSQL 9.1.4 - installed from amzn repo
*   Nginx 1.0.15 - installed from amzn repo
*   Git 1.7.4.5 - installed from amzn repo

The Mob has a current Chicago Boss installation (as of 20120904, something like a 0.8.0-special) and a current cb_admin installation (as of 20120904). 

Bcrypt is installed as an Erlang lib, so it is available to all applications, though it is not used in the demo code.

Mongo, MySQL, PostgreSQL, and Nginx all start automatically.

Chicago Boss is configured as a service, which loads from the cb_admin directory. Once you get to know CB, you'll find that this means CB loads boss.config from cb_admin (which is where you'll find all the databases configured as db_shards). Since it's configured as a service, you can do the following as the 'boss' user:

<pre>
sudo service chicagoboss start
</pre>

And then review the site in a browser. The service accepts start|stop|restart|reload commands. If you want to use 'dev', you should launch via '/home/boss/cb_admin/init-dev.sh'.

Enjoy, happy Chicago Bossing!
