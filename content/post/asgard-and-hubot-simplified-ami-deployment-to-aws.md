{
  "title": "Asgard and Hubot: Simplified AMI deployment to AWS",
  "description": "Asgard and Hubot: Simplified AMI deployment to AWS",
  "date": "2013-09-02",
  "url": "asgard-and-hubot-simplified-ami-deployment-to-aws",
  "type": "post",
  "tags": [
    "aws",
    "asgard",
    "netflix",
    "hubot"
  ]
}
If you are not familiar with these tools, you should spend some time investigating [Asgard](https://github.com/Netflix/asgard) (deployment management for AWS; from NetflixOSS) and [Hubot](http://hubot.github.com/) (extensible chatbot; from GitHub). 

### The Goods

[hubot-asgard (github)](https://github.com/imperialwicket/hubot-asgard) | [hubot-asgard (npm)](https://npmjs.org/package/hubot-asgard)

'npm install hubot-asgard' should get you most of the way there, check the readme on either github or npm for additional details about environment variables and configuration. Once installed, 'hubot help asgard' will show all the commands.

### The Problem

Concerns about AMI management and deployment to Amazon Web Services quickly lead to the nebulous AutoScaling Group feature of EC2 (why isn't this in the management console?). Once you find yourself with a handful of scripts using some sdk from Amazon that amount to launch configuration creation and autoscaling group updates with a dash of load balancer add/remove, you start to wonder if there's an easier way. There are several. [Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/) and [OpsWorks](http://aws.amazon.com/opsworks/) aim to address this problem, and they do a good job. The only thing I will add is that if you like fine control of your environment, you'll quickly move from Elastic Beanstalk to OpsWorks (in beta at time of writing), and if that starts to leave you wanting control, eventually you'll get to Asgard.

### Basics

Asgard is first and foremost a tool for managing AWS objects for Netflix. They do a great job of making the tool useful for everyone else, but keep in mind that Netflix is probably using (a lot) more AWS objects than you. For those of us who want to manage a couple application stacks with 10 or 20 servers during peak, running an extra medium instance and figuring out the basics of Asgard might seem excessive. Enter [hubot-asgard](https://github.com/imperialwicket/hubot-asgard). After setup, your chat room could look something like this:

*   me: asgard ami
*   hubot: [list of amis in your account]
*   me: asgard cluster
*   hubot: [list of clusters in your account with autoscaling group details]
*   me: asgard next some-cluster ami-abcd1234>
*   hubot: 'asgard task 1' or path_to_asgard_task
*   [After confirming that the new instances are satisfactory]
*   me: asgard cluster some-cluster
*   hubot: [list of autoscaling groups in the cluster]
*   me: asgard autoscaling activate new-asg
*   hubot: 'asgard task 2' or path_to_asgard_task
*   me: asgard autoscaling delete old-asg

This is much easier than managing AMI deployment manually, and you have all the tuning control that you want at the application level with Asgard. Since you don't need all these application level features everytime you want to push a new AMI, deployment with Hubot allows you to ignore the full Asgard installation much of the time. It still looks like a lot of typing though, right? These abbreviated commands work too, and accomplish the exact same outcome as the previous example:

*   me: a a
*   hubot: [list of amis in your account]
*   me: a c
*   hubot: [list of clusters in your account with autoscaling group details]
*   me: a next some-cluster ami-abcd1234
*   hubot: 'asgard task 1' or path_to_asgard_task
*   [After confirming that the new instances are satisfactory]
*   me: a c some-cluster
*   hubot: [list of autoscaling groups in the cluster]
*   me: a as activate new-asg
*   hubot: 'asgard task 2' or path_to_asgard_task
*   me: a as delete old-asg

### Maintaining Asgard

One concern might be running an Asgard instance(s) all the time. If you want to shut down Asgard, that's cool. Hubot-asgard comes with a few 'asgard-launcher' commands for managing your own Asgard instance. Start up a working Asgard from NetflixOSS's ami, create a custom AMI with your own credentials, and launch/terminate the instance via Hubot. Once hubot is configured with hubot-asgard, it looks like this:

*   me: asgard-launcher run
*   hubot: Pending instance i-abcd1234 is launching based on ami-a1b2c3d4.
*   hubot: Use 'asgard-launcher url' to retrieve the PublicDnsName.
*   hubot: Added tag Name=asgard-hubot to instance i-abcd1234
*   me: asgard-launcher url
*   hubot: Asgard is loading at http://aws-public-dns.com:8080/
*   hubot: Use 'asgard-launcher authorize YOUR_IP' to update the asgard-hubot security group and allow access over 8080 to your ip.
*   hubot: Use 'asgard url http://aws-public-dns:8080/' to save this dns value for hubot-asgard.
<li>hubot: After entering your AWS config data, use 'asgard-launcher create ami' to generate a private and configured AMI (it is recommended to DISABLE the public Amazon images options for hubot-asgard).
*   asgard-launcher create ami
*   Found instance: i-abcd1234
*   Created AMI ami-4d3c2b1a
*   me: asgard-launcher terminate

Hubot-asgard will automatically use the newly created ami next launch (using Redis storage). Don't forget to issue the 'asgard url http://public-dns:8080/' message so that Hubot knows where your Asgard instance can be reached. If you are terminating and re-launching Asgard, you will need to do this every time - and you should probably [create a bug about it](https://github.com/imperialwicket/hubot-asgard/issues).

### Deployment

The example above showed deployment based on a new autoscaling group. Note that the default treatment of hubot-asgard is to start new autoscaling groups without adding them to the configured load balancer. This is why the 'asgard autoscaling activate new-asg' command was necessary. If you want to push live instances with no further interaction, you can add a 'false' flag to the 'asgard next' command, to indicate removal of the 'trafficAllowed=true' parameter (EDIT: this had a reversed boolean in the original text).

*   me: asgard next some-cluster ami-abcd1234 false

You could also do a rolling push. Asgard allows you to simply provide a new AMI, and Asgard will roll the new AMI out one instance at a time. It's configurable, but hubot-asgard uses a basic one-at-a-time style similar to the configuration used in the rolling push on the [Asgard REST API](https://github.com/Netflix/asgard/wiki/REST-API) wiki page.

*   me: asgard rollingpush some-asg ami a1b2c3d4

I believe the rolling push updates the launch configuration and then uses timers to terminate older instances (I haven't checked, but it doesn't matter). DO NOT USE rolling push if you only have one instance running in an autoscaling group. It's a delete then replace action, not a replace then delete.

Just want to resize an autoscaling group in your application? No problem:

*   me: asgard autoscaling some-asg 4 25

### Finishing touches

First, sit back and do some image browsing with Hubot while you think about how much time you just saved by using Asgard to manage your AMI deployment.

Now spend some time reading the hubot-asgard help entries and seeing the rest of your options for Asgard interaction from Hubot. [Report any issues you find](https://github.com/imperialwicket/hubot-asgard/issues). 

Remember that output is all templated, if you want something else, either bug it or customize the eco templates in hubot-asgard/templates. 

Now you can start thinking about some of these challenges (if not already addressed):

1.  Ami preparation - Puppet, Chef, Ansible, Salt, maybe [Aminator](https://github.com/Netflix/aminator), then script it for Hubot
2.  Track changes to your AWS Objects with [Edda](https://github.com/Netflix/edda)
3.  Monitoring - Lots of tools for this
4.  Logging - I like the [Logstash](http://logstash.net/) stack
