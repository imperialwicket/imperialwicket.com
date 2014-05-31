{
  "title": "Netflix OSS: Building an Asgard AMI on AWS",
  "description": "Netflix OSS: Building an Asgard AMI on AWS",
  "date": "2013-07-16",
  "url": "netflix-oss-building-an-asgard-ami-on-aws/",
  "type": "post",
  "tags": [
    "aws",
    "asgard",
    "netflix"
  ]
}
### Prerequisites

*   AWS Account
*   AWS EC2 Keypair
*   AWS Access Key (IAM - not root account)
*   AWS Account Number
*   Some SSH familiarity

### Intro

I'm going to build an [Asgard](https://github.com/Netflix/asgard) instance from a baseline Ubuntu LTS, if you want to skip directly to a functioning Asgard AMI, [grab my latest Asgard AMIs here](http://imperialwicket.com/tag/asgard). 

The general technique for building our own Asgard instance is:

1.  Launch a baseline instance (EBS backed)
2.  Install Puppet and Git
3.  Acquire and apply a puppet manifest to install the JDK, Tomcat, and Asgard
4.  Configure and create an Asgard AMI
5.  Enhancements and next steps

### Launch an EC2 instance

I'm going to use Ubuntu 12.04.2 LTS, and launch it from the AWS Management Console. There won't be anything special about the launch, just keep in mind that you'll want to be able to open ports 22 and 8080 in your security group, and you'll need to use a configured Key Pair to access via ssh. I recommend an m1.small to get started, and for general usage you may want to upgrade to a medium. If you have a lot of AWS resources, you'll likely need to go larger.

### Install Puppet and Git

Use the [puppet repository instructions](http://docs.puppetlabs.com/guides/puppetlabs_package_repositories.html) appropriate for your distribution choice. On Ubuntu, I performed the Puppet and Git install as follows. Note that I put an 'apt-get upgrade' in there, you probably want to start with all the latest security patches when you build a new AMI. If there are kernel updates, you might want to restart the system before you get too far. Creating an AMI from an instance that is in need of a restart will potentially yield an AMI that doesn't launch.

<pre>
ssh -i /path/to/key ubuntu@ec2##-##-##-##.region.compute.amazon.com
wget http://apt.puppetlabs.com/puppetlabs-release-precise.deb
sudo dpkg -i puppetlabs-release-precise.deb
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install puppet git
</pre>

### Install JDK, Tomcat, Asgard via Puppet

We need to clone a couple puppet modules to handle Tomcat and Asgard, and we'll use a basic manifest to install the necessities. We also need to install the puppetlabs/stdlib module. I'm going to do this as root - it's never a good idea, but it's always so easy.

<pre>
sudo su -
cd /etc/puppet/modules/
git clone https://github.com/camptocamp/puppet-tomcat.git tomcat
git clone https://github.com/imperialwicket/puppet-asgard.git asgard
puppet module install puppetlabs/stdlib
cd /etc/puppet/manifests/
git clone https://github.com/imperialwicket/netflixoss-puppet-manifest-samples.git netflixoss
puppet apply /etc/puppet/manifests/netflixoss/asgard-basic.pp
service tomcat7 restart
</pre>

### Configure and create an Asgard AMI

After giving tomcat a chance to load the Asgard war (use 'top', tomcat will use all the CPU for a bit, then drop to under 1%), you can navigate to http://ec2##-##-##-##.region.compute.amazon.com:8080\. If you encounter issues here, check that tomcat is not still using all the CPU and confirm that your security group allows 8080 access.

Once you see the Asgard initialize page, provide your Secret Key and AWS Account ID and allow Asgard some time to cache your AWS resource data. After Asgard collects your data, you can investigate the tool. Whenever you're ready, use the AWS Management Console to create a new AMI from your instance.

### Enhancements and next steps

This is a very bare installation. You probably want to create at least one user, so that you aren't always connecting as 'ubuntu'. You should also upload a public key, so you are not relying on the AWS Key Pair. Once those are in place, some additonal configuration options might appeal to you. I prefer to proxy to tomcat via Apache/Nginx, configure SSL, and use basic auth. This way I can comfortably access data from potentially public networks without worrying too much. The [pre-made AMIs](http://imperialwicket.com/tag/asgard) I mentioned above have this implemented already, and I'll aim to add a more full-featured manifest in the future.

Before enhancing this Asgard instance, I want to start building a service with the already functional Asgard. In the next post I will detail using Asgard to launch and control specific AWS resources for a sample use case.
