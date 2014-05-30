{
  "title": "Netflix Asgard Ubuntu 12.04 LTS ami (Release 1.1.1.01)",
  "description": "Netflix Asgard Ubuntu 12.04 LTS ami (Release 1.1.1.01)",
  "date": "2013-05-19",
  "url": "netflix-asgard-ubuntu-1204-lts-ami-release-11101",
  "type": "post",
  "tags": [
    "aws",
    "asgard",
    "netflix"
  ]
}
*** UPDATE: Be sure to [check for updated AMIs](http://imperialwicket.com/tag/asgard). Some updates include newer Asgard versions, others include optimizations and enhancements to the AMI itself. ***

I am creating a number of Amazon Machine Images that include pre-configured [Netflix open source software.](http://netflix.github.io/) Asgard is the first target, and this is the first release. I plan to support at least Ubuntu and Amazon Linux; ideally CentOS and Debian will also have prepared AMIs.

### Netflix OSS: Asgard (Ubuntu 12.04LTS 1.1.1.01)

US-East (VA): ami-f7e28b9e
US-West (OR): ami-a5c25395

This is a pre-built, public AMI that provides Netflix OSS: Asgard. This AMI is free to use, and I will do my best to support and update it. For general Asgard/AWS questions, the [Asgard email group](https://groups.google.com/forum/?fromgroups#!forum/asgardusers) or [Asgard wiki](https://github.com/Netflix/asgard/wiki) are good starting points.

### Getting Started

You need an AWS account; you can find a lot of helpful basics on getting started with AWS on this site and elsewhere. This runs on a micro instance, but not well. I strongly recommend a small instance or greater, depending on how much your managing within AWS. Configure your security group to allow access to port 443 (HTTPS), if you want to change any configuration or connect via SSH you will need access to port 22 (SSH). 

By default, once this server is started you can access Asgard using the AWS public dns over HTTPS (and only HTTPS)\. Note that the AMI uses Ubuntu's default Apache certificate, you will need to acknowledge this certificate mismatch upon first visiting your AMI via web browser. If this is concerning (as it should be!), you should connect via SSH and either move to port 80 (default or no-auth) or add an appropriate certificate. If you update the hostname, you should add the desired ServerName to the appropriate Apache site configuration (though this is not strictly necessary). 

The initial basic auth creds are stored in /etc/apache2/.htpasswd and are:

<pre>user: netflix
pass: netflix</pre>

#### SSH Connection

You can connect as follows:
<pre>
ssh -i /path/to/aws/key.pem netflix@ec2-##-##-##-##.*.compute.amazonaws.com
</pre>

The netflix user has passwordless sudo privileges.

#### Web Server config

This ami is configured to proxy via Apache 2.2 (80 or 443) to Apache Tomcat 7 (8080). As noted, the default site for Apache is SSL only, and has basic authentication enabled. 

##### Clear the netflix basic authentication credentials:

<pre>> /etc/apache2/.htpasswd</pre>

##### Add basic authentication credentials:

** Keep a space at the beginning of this command **
<pre> htpasswd -b /etc/apache2/.htpasswd username password</pre>

There are four pre-configured sites available for apache:

*   default-ssl - Enabled; SSL connection with basic authentication
*   no-auth-ssl - SSL connection with no authentication
*   default - HTTP connection with basic authentication
*   no-auth - HTTP connection with no authentication

Use a2ensite/a2dissite to enable/disable these server configurations.

### Roadmap

The current plan is to create an initial Amazon Linux release, then finish full puppet modules/manifests for building the base AMI and the particular Netflix OSS AMIs. This allows community members a great deal of flexibility in building their own AMI and also in maintaining these releases. 

This AMI uses OpenJDK, which is in line with my personal preferences. If there is great demand for Oracle Java 6/7, I'll add a script to switch JDKs.

I will have the initial puppet repos on github soon, please report any issues or feature requests via [github](https://github.com/imperialwicket) or imperialwicket (at) gmail.
