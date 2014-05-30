{
  "title": "MySQL Workbench: Server Administration connection error",
  "description": "MySQL Workbench: Server Administration connection error",
  "date": "2011-11-23",
  "url": "mysql-workbench-server-administration-connection-error",
  "type": "post",
  "tags": [
    "mysql"
  ]
}
I have a love-hate (read: love-to-hate) relationship with MySQL Workbench. The one thing that I do love is that MySQL Workbench integrates SSH tunneling for remote db access, which more people need to use. Stop installing phpmyadmin on servers to which you have ssh access, and start using MySQL Workbench. If you are lucky enough to have a system that can still use the GUI tools, you can accomplish the same tunneling using SSH tunnels and an open port on your local machine. Already getting off-track though, back to the connection error.

I had not encountered this issue before, which is merely a lucky versioning dance. Somewhere along the development line, MySQL Workbench decided that the Server Administration connections would benefit from knowing what plugins are available during the initiation of the connection. Not a bad idea, but the implementation forgets to check that the server is 5.1 or greater before issuing, "show plugins;". Most new projects and hosting plans will use 5.1, but there are a lot of legacy systems running 5.0, and a few hosting providers who still get you started with 5.0 on their entry plans (I'm looking at you GoDaddy.). In these scenarios you may be using MySQL Workbench successfully on the SQL Development side, only to find that when you attempt to connect to the same database server for Server Administration, the connection fails with an odd error:

<pre>
Error Starting Workbench Administrator
QueryError: Error executing 'You have an error in your SQL 
syntax; check the manual that corresponds to your MySQL
server version for the right syntax to use near 'plugins' at line 1'
show plugins.
SQL Error: 1064
</pre>

I am using MySQL Workbench version 5.2.35 rev 7915 in Fedora, but the error should be similar on other operating systems. It took me a while to dig up [this bug report](http://bugs.mysql.com/bug.php?id=62549), which is logged against Windows but has a good account of the issue - and a resolution. 

#### The Fix

MySQL Workbench is plugin oriented (debatably one of the other benefits the Workbench has over the old GUI tools) and plugins are written in Python. The resolution proposed in the bug report is to add a simple check before the 'show plugins;' to avoid running this statement against 5.0.x servers. To do this:

1.  Locate the 'wb_admin_control.py' file. This is in '/usr/lib64/mysql-workbench/modules/wb_admin_control.py' on 64 bit Fedora 14\. Windows should be somewhere like 'C:\Program Files\MySQL\MySQL Workbench 5.2 CE\modules\wb_admin_control.py', but I have not confirmed this location.
2.  Open this is a text editor and make the following change:

Original:
<pre>
            # check version
            result = self.exec_query("select version() as version")
            if result and result.nextRow():
                self.server_version= result.stringByName("version")
                self.raw_version = self.server_version
                dprint_ex(1, "Got MySQL version -", self.server_version)

            result = self.exec_query("show plugins")
            # check whether Windows authentication plugin is available
            while result and result.nextRow():
                name = result.stringByName("Name")
                status = result.stringByName("Status")
                plugin_type = result.stringByName("Type")
                if status == "ACTIVE":
                    self.server_active_plugins.add((name, plugin_type))
</pre>
Updated:
<pre>
            # check version
            result = self.exec_query("select version() as version")
            if result and result.nextRow():
                self.server_version= result.stringByName("version")
                self.raw_version = self.server_version
                dprint_ex(1, "Got MySQL version -", self.server_version)

            # don't check plugins on servers running 5.0
            if self.server_version.startswith("5.0."):
                result = self.exec_query("show plugins")
                # check whether Windows authentication plugin is available
                while result and result.nextRow():
                    name = result.stringByName("Name")
                    status = result.stringByName("Status")
                    plugin_type = result.stringByName("Type")
                    if status == "ACTIVE":
                        self.server_active_plugins.add((name, plugin_type))
</pre>

3.  Close and reopen MySQL Workbench and watch your connection succeed.

Hopefully an update to MySQL Workbench will address this, but I'm not holding my breath.  Happy administrating.
