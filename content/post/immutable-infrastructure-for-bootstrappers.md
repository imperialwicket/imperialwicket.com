{
  "title": "Immutable Infrastructure for Bootstrappers",
  "description": "Immutable Infrastructure for Bootstrappers",
  "date": "2015-07-22",
  "url": "Immutable Infrastructure for Bootstrappers/",
  "type": "post",
  "tags": ["immutable infrastructure","aws","packer","asgard","vagrant"]
}

Companies large and small are singing praises for infrastructure as code and immutable infrastructure. Younger companies are using tools around Docker and containers to promote and mandate immutable infrastructure. These are all great, but if you are a lone bootstrapper burning the midnight oil, it does not seem like the end justifies the means (at least not to me). Not only do these tools require constant upkeep, as this domain is so rapidly changing, but there are often subtle and intricate 'features' that spring up when particular versions are paired, or on some Linux kernels, or with some file system types, or, or, or. As a developer who spends a lot of time promoting immutable infrastructure on the job, I really wanted to find a practical way to achieve similar results for my bootstrapping world. This meant attention to the following concerns:

  1. Low cost - I don't want to run a bunch of extra servers and effectively double my costs in order to make things 'more efficient' at a systems level, nor do I want to pay meaningful sums of money so that someone else can run those servers for me.
  2. Low barrier to entry - Configuration management is a good example here: I love configuration management, but it takes time. Time (money) is one thing that is at a premium for most bootsrappers, and many bootstrapped projects I have worked on or reviewed have questionable configuration management implementations. I think this is fine. As long as you can get up and running reliably, great. Low barrier - I do not want to learn new tools, buy new software, or make meaningful changes to my software in order to support this.
  3. Established technology - Running your bootstrapped project on new and hot tech is fun and interesting, but time and time again developers report that boring and mature technology gets things up and running faster. 
  4. Self-hosted/private - I don't want to use 'free for open source' solutions, because I want at least some of my bootstrapped code to be private. I love open source software, and I have a lot of code posted publicly - but I do not want to post this publicly just to be able to use decent SAAS tooling. Many SAAS tools in this space have private offerings, but remember (1).

## Solution: Packer, Vagrant, VirtualBox, Asgard, AWS

 - [Packer](https://packer.io/) - [HashiCorp](https://hashicorp.com/) project for creating system snapshots, capable of outputting many formats including Amazon Machine Images and Virtual Box boxes (useful for me).
 - [Vagrant](https://www.vagrantup.com/) - Great tool for virtual machine management, also from [HashiCorp](https://hashicorp.com/).
 - [VirtualBox](https://www.virtualbox.org/) - A well established virtualization product, to be managed by Vagrant.
 - [Asgard](https://github.com/Netflix/asgard) - Deployment and cluster management tool from [Netflix Open Source Software](http://netflix.github.io/).
 - [Amazon Web Services](https://aws.amazon.com/) - A forerunner in cloud service/hosting providers and the IAAS space.

In the context of our requirements

### Low cost

Packer, Vagrant, VirtualBox, and Asgard are all free and open source products. While Amazon Web Services is certainly not the cheapest way to host your infrastructure, it is one of the cheapest ways to quickly and easily convert a project to using immutable infrastructure. In this context, as well as the growth opportunities AWS offers, this is a cost burden I am willing to accept.

### Low barrier to entry

VirtualBox is cross-platform and has easy installation. With Vagrant installed, using VirtualBox [becomes even easier](https://docs.vagrantup.com/v2/cli/index.html). 

Packer is Go project, and it is distributed as a bundle of pre-compiled binaries. Even so, I plan to include packer binaries and some boilerplate packer config files in a project that accommodates my own needs for immutable infrastructure on a dime. There is a challenge here in that you must somehow convert your infrastructure to code, but Packer leaves a bootstrapper in good shape to implement this in the most convenient way that [Packer supports](https://packer.io/docs/) (shell, [Chef](https://www.chef.io/), [Puppet](https://puppetlabs.com), [Ansible](http://www.ansible.com/home), [Salt](http://saltstack.com/), [Custom](https://packer.io/docs/provisioners/custom.html)). If everything is hand-implemented, probably flipping over to a handful of shell scripts will be fastest, though some of those other tools are certainly worth investigation when there is time (as if...).

Asgard is a JVM project, distributed as a standalone jar file or a deployable war. It requires some knowledge of the JVM and servlet containers, and you also benefit from having basic Amazon Web Services familiarity. Alongside the packer installation, I am including Asgard install and a walk-through for getting up and running in the previously mentioned project.

AWS is daunting to newcomers. This is unfortunate, but I am willing to make an exception for AWS here since we really only need to stumble through some basic AWS tasks as a preliminary step. Once we configure a user and retrieve their credentials, Asgard takes care of the bulk of our AWS needs.

### Established technology

The newest player here is Packer, and while I have certainly hit some bumps in earlier versions Packer is moving along nicely and has well-established support for basic Amazon Machine Image (AMI) creation needs. Things can get tricky if you are using brand new AWS features or less-popular Packer options, but those are easy to work around and for a basic AMI builder they should not pose any challenges.

Vagrant and VirtualBox have been staples for my development for many years and they continue to get better. I can not imagine developing for the web without Vagrant and some virtualization technology.

Asgard was open sourced by the Netflix OSS team in 2012, and was already powering infrastructure internally. It manages a lot of production servers for a lot of companies.

AWS is always in the mix when we discuss cloud technologies and/or providers. Their backwards compatibility in the face of relentless product enhancement and growth is impressive. Even though AWS is mandated by the Asgard decision, it would be a strong contender nonetheless.

### Self-hosted/Private

The real concern here is Asgard (or any comparable solution). I am going to install and run this locally, so it is at least as private as my project code.


## Implementation

My solution is to create a virtual machine for local use that will interact with my AWS account from my development machine. I plan to have a single vm for my project, ideally created with some configuration management system. I can use this for development and testing, and when I am ready to deploy I can run a separate virtual machine with this immutable infrastructure toolset. 

The infrastructure management virtual machine (also created with some configuration management system) will run Asgard and Packer. I am going to keep it simple and use Asgard over the default tomcat port on the virtual machine, and ssh into the virtual machine to kick off Packer builds. Once Packer builds an AMI, I can locate the AMI in Asgard and trigger a deployment.

I made noteworthy concessions in this solution:

  1. Effective use of Packer requires some form of configuration management. If you are not doing this already, that is not an enhancement to be taken lightly. Nonetheless, I think it is a worthwhile venture, so long as it does not morph into weeks learning configuration management solution x. Just write some shell scripts and trade up when they get to be too annoying.
  2. AWS is costly and challenging. If you want to run immutable infrastructure, you are fairly limited to cloud offerings or running your own virtualization on bare metal. In that context, AWS is competitive. While you can certainly host your project for cheaper, I am not confident you can do so AND support immutable infrastructure. Likewise, while AWS poses challenges to newcomers, it is not substantially different from the necessary learning curve to OpenStack or other commercial cloud hosting services. It also tends to be the most well-documented. 

## Next steps

This is getting cumbersome, so I plan to break it into 4 or 5 posts. They will look like

 - Introduction
 - An immutable infrastructure virtual machine
 - Basic AMI generation with Packer
 - Deploying your AMI with Asgard
 - Releases, automation, and next steps

If you made it this far, you might enjoy a book I am writing on this topic: [Immutable Infrastructure with Netflix OSS](https://leanpub.com/immutable-infrastructure-with-netflixoss).





