{
  "title": "Immutable Infrastructure for Bootstrappers: Building AMIs with Packer",
  "description": "Immutable Infrastructure for Boostrappers Part 3: Building AMIs with Packer",
  "date": "2015-08-10",
  "url": "immutable-infrastructure-for-bootstrappers-building-amis-with-packer/",
  "type": "post",
  "tags": ["immutable infrastructure","aws","packer","asgard","vagrant"]
}

This post is part of series on my implementation of immutable infrastructure for bootstrapped projects. You can find the other posts here:

 - [Introduction](/immutable-infrastructure-for-bootstrappers/)
 - [An immutable infrastructure virtual machine](/immutable-infrastructure-for-bootstrappers-asgard-and-aws) 
 - [Basic AMI generation with Packer](/immutable-infrastructure-for-bootstrappers-building-amis-with-packer)
 - Deploying your AMI with Asgard
 - Releases, automation, and next steps

If you find this implementation interesting, you will enjoy a book I am writing on this topic: [Immutable Infrastructure with Netflix OSS](https://leanpub.com/immutable-infrastructure-with-netflixoss).

## Packer

If Packer is a new tool for you, be sure to check out the [Introduction](https://packer.io/intro) from HashiCorp. A basic premise is that you can configure your 'server' once, and Packer can output that server in whatever format you desire (AMIs in our case, and potentially virtualboxes for the same config in our development environment. We will work with the same [immutable-infrastucture-for-bootstrappers](https://github.com/imperialwicket/immutable-infrastructure-for-bootstrappers) repository, and as we previously mentioned, Packer is already available for use on the Vagrant-configured virtual machine.

## Amazon Web Services IAM user and profile

Like Asgard, we must provide appropriate IAM credentials to Packer in order to use the [amazon-ebs builder](https://packer.io/docs/builders/amazon-ebs.html). This builder requires capabilities like launching an EC2 instance, creating security groups, etc. Packer provides a minimal [IAM profile](https://packer.io/docs/builders/amazon.html) for using the Amazon builders. More explicit instructions for generating IAM users and assigning profiless are in the [previous post](/immutable-infrastructure-for-bootstrappers-asgard-aws).

## Using Packer in the Virtual Machine

We detailed Vagrant/VirtualBox and cloning the repository in an earlier post. With a running vm, you can use `vagrant ssh` to connect and then run the `/vagrant/bakery/ii4b-packer.sh` script.

The [ii4b-packer script](https://github.com/imperialwicket/immutable-infrastructure-for-bootstrappers/blob/master/bakery/ii4b-packer.sh) first looks for an expected credentials file, and prompts for AWS credentials if it is missing (exactly like the Asgard config script). By default the script will attempt to build an ami using the bakery base + bare configs. 

## `ii4b-packer.sh` and `packer-amazon-ebs.json`

The ii4b-packer script sets AWS credentials and wraps a number of Packer options for convenience. Use `ii4b-packer.sh -h` for more details on available options (Environment, Node, base AMI, etc.). This walk through implements an Environment-Node convention that has worked for me in many use cases, and tends to be well supported by a variety of alternate/supplemental services that might prove convenient down the road. By default the ii4b-packer script will try to build a 'bare' node for the 'ii4b' environment, based on an Ubuntu 14.04LTS ami.

The ii4b-packer script will also generate a new ssh key for the 'ii4b' user. This user/key is placed on the ami using the '[base](https://github.com/imperialwicket/immutable-infrastructure-for-bootstrappers/blob/master/bakery/base/base.sh)' configuration. This 'base' configuration represents another convention that has worked well for me, which is to use a base or foundational configuration (sometimes even as a pre-built 'foundation' AMI) that you can supplement on a per-node basis. 'Base' configuration can include things like logging, users, global package requirements, etc. This keeps final AMI generation times to a minimum, since much of the configuration is completed in the base AMI. You can modify the username within the `ii4b-packer.sh` and `base.sh` scripts, or provide an alternate key within the git-ignored bakery/keys/USERNAME/KEY location.

Our `packer-amazon-ebs.json` Packer configuration file is a basic Amazon EBS-backed AMI configuration with a number of user variables added and a pair of provision concepts ('base' - mentioned previously, and 'node' - for supporting dynamic nodes and adding node-specific config). The goal of this packer configuration file is that it should accommodate all of your needs, without having to deal with separate Packer config json files.

## Packer provisioners

I use the file and shell provisioners in the VM example. It is not my recommendation that you use these provisioners, however, I think they pose the lowest barrier to entry for any users who are not already using some specific provisioning tool. It is also my experience that Puppet/Chef/Ansible/Salt users are capable of converting from shell without much concern, whereas users relying on shell scripts will have more of a challenge picking up a provisioning-specific tool.

Most alternative provisioners from Packer support a 'node' concept in some form and many also support environments. If you elect to modify these tools to use a non-shell provisioner (which I highly recommend), I strongly urge you to maintain an environment-node concept within the provision. This keeps your packer configurations down, and maintains a very standard AMI generation process that easily scales as your environments and node types increase.

Note that the provided shell provisioners are Debian/Ubuntu specific, and if you require an alternative Linux, modifications will be necessary.

## Building an AMI

If you ran the `ii4b-packer` script and provided valid IAM credentials, chances are good that you already have an ii4b-bare-TIMESTAMP ami in us-east. If you have not, you can run `/vagrant/bakery/ii4b-packer.sh` to build a default 'bare' AMI. After providing credentials, you should see output like: 

````
vagrant@ii4bootstrappers:~$ /vagrant/bakery/ii4b-packer.sh 
ii4b output will be in this color.

==> ii4b: Prevalidating AMI Name...
==> ii4b: Inspecting the source AMI...
==> ii4b: Creating temporary keypair: packer ------------
==> ii4b: Creating temporary security group for this instance...
==> ii4b: Authorizing access to port 22 the temporary security group...
==> ii4b: Launching a source AWS instance...
    ii4b: Instance ID: i-55555555
==> ii4b: Waiting for instance (i-55555555) to become ready...
==> ii4b: Waiting for SSH to become available...
==> ii4b: Connected to SSH!
==> ii4b: Uploading ./base => /tmp
==> ii4b: Provisioning with shell script: base/base.sh
    ii4b: Adding user `ii4b' ...
    ii4b: Adding new group `ii4b' (1001) ...
    ii4b: Adding new user `ii4b' (1001) with group `ii4b' ...
    ii4b: Creating home directory `/home/ii4b' ...
    ii4b: Copying files from `/etc/skel' ...
==> ii4b: Uploading ./bare => /tmp
==> ii4b: Provisioning with shell script: bare/bare.sh
    ii4b: Bare node installs 'base' and does nothing else by default.
==> ii4b: Stopping the source instance...
==> ii4b: Waiting for the instance to stop...
==> ii4b: Creating the AMI: ii4b-bare-1439225739
    ii4b: AMI: ami-bbbbbbbb
==> ii4b: Waiting for AMI to become ready...
==> ii4b: Adding tags to AMI (ami-bbbbbbbb)...
    ii4b: Adding tag: "Name": "ii4b-bare-1439225739"
==> ii4b: Tagging snapshot: snap-99999999
==> ii4b: Terminating the source AWS instance...
==> ii4b: Cleaning up any extra volumes...
==> ii4b: No volumes to clean up, skipping
==> ii4b: Deleting temporary security group...
==> ii4b: Deleting temporary keypair...
Build 'ii4b' finished.

==> Builds finished. The artifacts of successful builds are:
--> ii4b: AMIs were created:

us-east-1: ami-bbbbbbbb
vagrant@ii4bootstrappers:~$ 
````

Now you can load Asgard, proceed to the AMI area, and search 'ii4b' to see your AMI(s).

![Asgard Bare AMI](/files/asgard-bare-ami.png)

The default bare AMI is not particularly useful within the concepts of Asgards App/Cluster, since it does not really provide any useful services. While not much better, you can get a glimpse of where we are heading by building a 'web' node using:

`/vagrant/bakery/ii4b-packer.sh -n web`

## Next steps

Now that we have Packer configured and can successfully build AMIs, we can move to generating more realistic AMIs and deploying them via Asgard. We will generate a sample database/back-end AMI and a modified web AMI that actually uses it.
