{
  "title": "Netflix Asgard Amazon Linux 201303 (Release 1.1.1.01)",
  "description": "Netflix Asgard Amazon Linux 201303 (Release 1.1.1.01)",
  "date": "2013-05-22",
  "url": "netflix-asgard-amazon-linux-201303-11101/",
  "type": "post",
  "tags": [
    "aws",
    "asgard",
    "netflix"
  ]
}
*** UPDATE: Be sure to [check for updated AMIs](http://imperialwicket.com/tags/asgard). Some updates include newer Asgard versions, others include optimizations and enhancements to the AMI itself. ***

### Netflix OSS: Asgard Amazon Linux (1.1.1.01)

US-East (VA): ami-17761e7e
US-West (OR): ami-1bfe6f2b

This is a pre-built, public AMI that provides Netflix OSS: Asgard on Amazon Linux. This AMI is free to use, and I will do my best to support and update it. For general Asgard/AWS questions, the [Asgard email group](https://groups.google.com/forum/?fromgroups#!forum/asgardusers) or [Asgard wiki](https://github.com/Netflix/asgard/wiki) are good starting points.

### Getting Started

This AMI very closely matches the [Ubuntu-flavored Netflix Asgard AMI](http://imperialwicket.com/netflix-asgard-ubuntu-1204-lts-ami-release-11101) that is available on Amazon Web Services. There is a lot of helpful information there regarding general connectivity and configuration options. 

I will relay here that the default launch configuration automatically starts apache and tomcat, and requires access to port 443\. The SSL certificate is generic (you'll need to confirm that it's ok), and the site is hidden behind basic authentication. The initial basic auth creds are stored in /etc/httpd/.htpasswd and are:

<pre>user: netflix
pass: netflix</pre>

### SSH Connection

You can connect as follows:
<pre>
ssh -i /path/to/aws/key.pem netflix@ec2-##-##-##-##.*.compute.amazonaws.com
</pre>
