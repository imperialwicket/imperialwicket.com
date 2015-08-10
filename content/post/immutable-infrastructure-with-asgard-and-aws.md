{
  "title": "Immutable Infrastructure for Bootstrappers: Asgard and AWS",
  "description": "Immutable Infrastructure for Boostrappers Part 2: Asgard and AWS",
  "date": "2015-07-27",
  "url": "immutable-infrastructure-for-bootstrappers-asgard-and-aws/",
  "type": "post",
  "tags": ["immutable infrastructure","aws","packer","asgard","vagrant"]
}

This post is part of series on my implementation of immutable infrastructure for bootstrapped projects. You can find the other posts here:

 - [Introduction](/immutable-infrastructure-for-bootstrappers/)
 - [An immutable infrastructure virtual machine](/immutable-infrastructure-for-bootstrappers-asgard-aws) 
 - [Basic AMI generation with Packer](/immutable-infrastructure-for-bootstrappers-building-amis-with-packer)
 - Deploying your AMI with Asgard
 - Releases, automation, and next steps

If you find this implementation interesting, you will enjoy a book I am writing on this topic: [Immutable Infrastructure with Netflix OSS](https://leanpub.com/immutable-infrastructure-with-netflixoss).

## VirtualBox and Vagrant

We need a virtual machine provider and Vagrant for managing the virtual machines. I will be using [VirtualBox](https://www.virtualbox.org/)  (4.3.x on Linux) and [Vagrant](https://www.vagrantup.com/) (1.7.x). Install these, and some familiarity with [vagrant's commands](https://docs.vagrantup.com/v2/cli/index.html) (up, provision, ssh, halt, and destroy in particular) will be helpful. I will try to give all the commands explicitly, but I will not spend much time explaining what they are accomplishing.

## Amazon Web Services IAM user and profile

As detailed in the previous post, we are running Asgard (and eventually Packer) in a virtual machine. Before we can run Asgard effectively on the virtual machine, we require IAM credentials and an Amazon Web Services account id. I recommend using [AWS: IAM->Users](https://console.aws.amazon.com/iam/home?#users) to create a user specific to Asgard. Create a user with keys, and then assign an inline policy like [this one](https://gist.github.com/imperialwicket/3779b9fb7d9fb4b9f781). That policy is taken from the [Asgard wiki](https://github.com/Netflix/asgard/wiki/Amazon-IAM-Role-Policies), but uses more open autoscaling and ec2 policies to allow for a single policy (Amazon Web Services limits the size of IAM policies). Your account id is available via the [AWS Management Console](https://console.aws.amazon.com/billing/home?#/account) when logged in as your root user.

## Launching and Configuring the Virtual Machine

With AWS credentials available, we can clone my [immutable infrastructure for bootstrappers](https://github.com/imperialwicket/immutable-infrastructure-for-bootstrappers) project, and get started:

````
git clone git@github.com:imperialwicket/immutable-infrastructure-for-bootstrappers.git
cd immutable-infrastructure-for-bootstrappers
vagrant up
````

Reviewing `Vagrantfile` and `bootstrap.sh`, we see that Vagrant imports an Ubuntu/Trusty64 image and then runs the bootstrapping script. Bootstrapping consists of updating apt, installing a jdk and tomcat, grabbing the latest Asgard and Packer, and copying some scripts/configs. This process will take a few minutes; when completed you should see something like:

````
==> ii4bootstrappers: Installing Asgard release 1.5.1.
==> ii4bootstrappers:  * Stopping Tomcat servlet engine tomcat7
==> ii4bootstrappers:    ...done.
==> ii4bootstrappers: Tomcat stopped until IAM credentials are available.
==> ii4bootstrappers: Installing Packer version 0.8.2.
==> ii4bootstrappers: 
==> ii4bootstrappers: 
==> ii4bootstrappers: ######################################################################
==> ii4bootstrappers: #
==> ii4bootstrappers: # Use /vagrant/asgardConfig/set-aws-creds.sh to
==> ii4bootstrappers: # configure your credentials for Asgard.
==> ii4bootstrappers: #
==> ii4bootstrappers: ######################################################################
````

Now you can use `vagrant ssh` to connect to the virtual machine, and run the `/vagrant/asgardConfig/set-aws-creds.sh` script to insert your AWS credentials in the appropriate places. Before blindly providing AWS credentials (that happen to be fairly powerful in this scenario), you should probably double check the code to see what is [being done with them](https://github.com/imperialwicket/immutable-infrastructure-for-bootstrappers/blob/master/asgardConfig/set-aws-creds.sh#L28). Running the script should look like:


````
vagrant@ii4bootstrappers:~$ /vagrant/asgardConfig/set-aws-creds.sh 
###########################################################
#
#  Please provide the following:
#    - AWS account id:
#       (https://console.aws.amazon.com/billing/home?#/account)
#    - AWS Access Key ID
#    - AWS Secret Access Key
#
#  Sample IAM profile for an IAM user specific to Asgard:
#    (https://gist.github.com/imperialwicket/3779b9fb7d9fb4b9f781)
#
#  If you require config changes you can manually edit or
#  delete the /usr/share/tomcat7/Config.groovy file 
#  (deleting will cause this script to run again).
#
###########################################################

AWS account id: 555555555555
AWS access key id: AKIAAAAAAAAAAAAAAAAA
AWS secret access key: 

Inserting credentials in /var/lib/tomcat7/Config.groovy.
 * Starting Tomcat servlet engine tomcat7                                                                                             [ OK ] 

Asgard will be available from your host machine at:
  http://localhost:8181

vagrant@ii4bootstrappers:~$ 
````

If your Access Key Id and Secret Access Key are accurate and the IAM policy is appropriate, you should be able to navigate to [http://localhost:8181] on your host machine and see the Asgard web interface:

![Asgard Home](/files/asgard-home.png)

The UI takes a little getting used to, but we are only going to need a small portion of Asgard's features to manage a small bootstrapped-inspired project. However, I think there is very little overhead to deploying and managing AMIs in this way, and your project will need to grow very substantially before Asgard starts to become a scalability issue (running it on a VM would probably need to change before it got that far).

### Troubleshooting

Inevitably things can go wrong. The usual culprits here are:

 - Inaccurate AWS credentials (Asgard can not fetch any data from/about your AWS account due to permissions)
 - Inappropriate IAM profile (Asgard gets errors from AWS that it can not complete a request due to permissions)
 - Resource issues for Tomcat (OOM, kernel shut down)

More information is likely available in `/var/log/tomcat7`. `Catalina.out` and `asgard.log` are particularly useful.


## Next steps

In the next post we will review some of the Asgard-specific concepts (Apps and Clusters), generate a working AMI building process with [Packer](https://packer.io/), and get everything ready to deploy the first version of a very basic sample application.
