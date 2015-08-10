{
  "title": "Netflix Asgard 1.2 AMI Updates",
  "description": "Netflix Asgard 1.2 AMI Updates",
  "date": "2013-07-12",
  "url": "netflix-asgard-12-ami-updates/",
  "type": "post",
  "tags": [
    "aws",
    "asgard",
    "netflix"
  ]
}
Update (2015-08-10): Lately I have been using a very basic Vagrant/Virtualbox config for Asgard needs. This config is easily applied to an Amazon instance, and you might be interested in the details of [Using Asgard in a virtual machine](/immutable-infrastructure-for-bootstrappers-asgard-and-aws).

Updated Asgard 1.2 AMIs are available for Ubuntu 12.04.2LTS and Amazon Linux 2013.03\. These AMIs include system updates as well as the 1.2 Asgard war.

These AMIs default to HTTPS only (so you're security group needs at least 443 access from your IP; add port 22 if you want to make changes via SSH), and use basic authentication to protect access. Basic authentication is configured with user 'netflix' and password 'netflix'. Add your IAM creds, and let Asgard do the work! Note that micro instances might have some trouble running this. Connect over SSH as user 'netflix' using your configured keypair. More details about the instance config are available in an [earlier post](http://imperialwicket.com/netflix-asgard-ubuntu-1204-lts-ami-release-11101) under Getting Started.

###  Netflix OSS: Asgard 1.2 - AMIs

Asgard 1.2: Ubuntu 12.04LTS -

*   US-East (VA): ami-acadd2c5
*   US-West (OR): ami-dd7be8ed

Asgard 1.2: Amazon Linux - 

*   US-East (VA): ami-90add2f9
*   US-West (OR): ami-c17be8f1

These are new AMIs primarily for adding Asgard 1.2, and addressing some security-related system updates. 

Also, I want to highlight that if you are looking for AMIs with a little more hands-on required (or just looking for AMIs built with [Ansible](https://github.com/ansible/ansible/) and the corresponding playbooks), check out the [ansible playbooks](https://github.com/awsanswers/netflixoss-ansible)  Peter S. put together for [Asgard](https://github.com/Netflix/asgard) and other Netflix Oss projects. Peter's Asgard image is a little more barebones, and that will definitely be right for some users. His Ansible playbooks should support all the major RHEL and Debian derivatives - but I believe he's also focusing efforts on support for Ubuntu and Amazon Linux. More good data on his AMIs is on [GitHub](https://github.com/awsanswers/netflixoss-ansible).

I'm going to post some step by step guides for getting started with these AMIs next, and try to document my path as I use these AMIs and other Netflix OSS solutions to power a couple projects.

Again, please don't hesitate to contact me with enhancements or questions (@nw_iw). 
