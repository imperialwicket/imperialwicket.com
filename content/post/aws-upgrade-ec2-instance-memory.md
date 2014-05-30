{
  "title": "AWS: Upgrade EC2 instance memory",
  "description": "AWS: Upgrade EC2 instance memory",
  "date": "2010-12-05",
  "url": "aws-upgrade-ec2-instance-memory",
  "type": "post",
  "tags": [
    "aws"
  ]
}
Prepare for disappointment; you can not hot-swap memory on a running EC2 instance.  But since I get a lot of requests for this action, I thought I would title the post this way, and highlight what you can do to handle this situation.  The way to upgrade memory on an EC2 instance is to generate a new instance using the same AMI and storage with a larger amount of memory.  I'll detail the steps for an EBS backed instance (if you're using an S3 backed instance, you should already be pretty familiar with backup and mounting procedures.  Hence the upgrade is just generating a new instance, and performing the same mounts, and moving data from a backup).  

If you're concerned about EBS vs S3 backed instances, check out [this article from theserverlabs](http://www.theserverlabs.com/blog/2010/07/08/ec2-persistence-strategies/), they detail the pros and cons of each and offer a number of usage cases.  My guess is that most readers at this level are concerned about ease of use, and EBS backed instances offer a much greater ease of use for a very small price (you pay for allocated disk space in an EBS volume vs used disk space in your S3 buckets).  Currently at $0.10 USD per gigabyte month, I do not mind paying for the conveniences (and some of this is free if you are taking advantage of the Free Usage Tier).

If you happen to be investigating and using the Free Usage Tier - make sure that when creating a new instance, you stick with Micro, and merely simulate failing over to more memory (failing over to a different instance is an effective test).  Also, you will likely incur small charges for if you leave two micro instances running simultaneously and do not shut both down for an equivalent amount of time within the month.  Finally, be sure to remove your AMI, EBS Snapshot, and Elastic IP when you are finished them.  Total costs will be minimal (around $30 USD, leaving multiple Micro instances running for most of the month) if you forget or avoid doing these things, but I thought I would mention it.

So, the process goes: backing up your EC2 instance, starting a new instance, and failing over.  

#### Backing up your EC2 instance

If you do not already have an EBS backed EC2 instance - [start one](http://imperialwicket.com/aws-building-a-lamp-instance).  In order to make the transition nicer, be sure to put the instance behind an Elastic IP.  For scalability, a Load Balancer would be better, but that is beyond the scope of this post.  To add an Elastic IP, go to the Amazon EC2 tab in the AWS Management Console, select "Elastic IPs" under the Networking & Security section, and use the "Allocate New Address" option.  This generates a new (persistent) IP address, map this address to your instance using the "Associate" button, and within a couple minutes your instance becomes available at that IP.

With an EBS backed EC2 instance, all you need to do to generate a backup is select the instance from your "My Instances" list, and use the "Instance Actions" drop-down to "Create Image (EBS AMI)" (this command is also available when right clicking on an EC2 instance in your list).  You must enter an image name, and an optional description.  Note that this will restart your current instance and cause several minutes (usually 5-15 in my experience) of downtime.  There are less obtrusive ways to accomplish this, but they require the use of more advanced tools than the AWS Management Console, also issues with cached data and valid drive snapshots become a concern.  

#### Starting a new EC2 instance from your AMI

Once this completes AWS will have generated an AMI and an EBS Snapshot (which you can review in the AWS Management Console).  You can now launch a new instance, this time allocating more memory to the instance.  Note that an AMI is limited to instance types sharing its original platform, so a 32-bit AMI can never be used with a 64-bit instance (and vice versa).  In the Request Instances Wizard, use the "My AMIs" tab to select the image you just created.  After launching the new instance with greater memory, you should have two instances running and two identical EBS volumes backing those instances.  

#### Fail-over using Elastic IPs

Now that the greater memory instance is running, go back to the Elastic IPs section, and find the IP address that is associated with the original instance.  Disassociate the instance from the IP address, and Associate the new instance with greater memory.  Again, the IP update usually completes within a few minutes.

Now you have an EC2 instance consistently configured with your previous instance, but with more memory.  How do you manage this without any downtime?  My technique is to follow a more formal deployment plan.  Immediately before a server instance goes "live" (or into a scenario where downtime becomes unacceptable), make an AMI (the making of the AMI is the step that required downtime).  When you need to make updates, create a development instance for testing and implementation using that AMI.  Once the development instance is finalized, make a new AMI representing the changes.  Use the new AMI to generate a replacement live instance, and then use it as a baseline for generating future development instances.  That same AMI is also perfect for generating additional instances for load balancing.  Using a simple technique like this allows memory upgrades and instance updates to occur with no downtime.  
