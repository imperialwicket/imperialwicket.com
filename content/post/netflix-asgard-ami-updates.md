{
  "title": "Netflix Asgard AMI Updates",
  "description": "Netflix Asgard AMI Updates",
  "date": "2013-06-06",
  "url": "netflix-asgard-ami-updates/",
  "type": "post",
  "tags": [
    "aws",
    "asgard",
    "netflix"
  ]
}
*** UPDATE: Be sure to [check for updated AMIs](http://imperialwicket.com/tags/asgard). Some updates include newer Asgard versions, others include optimizations and enhancements to the AMI itself. ***

Updated Asgard AMIs are available for Ubuntu 12.04.2LTS and Amazon Linux 2013.03\. These are minor updates, and while I'm deprecating the original releases, there's no reason to upgrade if you are using the previous AMI with no issues.

###  Netflix OSS: Asgard 1.1.2 - AMIs

Asgard 1.1.2: Ubuntu 12.04LTS -

*   US-East (VA): ami-11453178
*   US-West (OR): ami-233fae13

Asgard 1.1.2: Amazon Linux - 

*   US-East (VA): ami-0347336a
*   US-West (OR): ami-753fae45

### Change Log

Amazon Linux:

*   Update motd.tail (add twitter contact)
*   Remove public ssh key

Ubuntu

*   Update motd.tail (correct Asgard version)
*   Update motd.tail (add twitter contact)

As these are non-critical modifications, I'm not going to delete the previous AMIs at this time. As I enhance standardization to the AMI build process and make more substantial updates, I'll begin removing the public AMIs for legacy releases. Note that if you launch an instance from a public AMI and delete the AMI or alter its permission scheme, the instance continues to run until terminated. This action will only keep additional instances from launching based on the legacy AMIs.

Please don't hesitate to contact me with enhancements or questions. 
