{
  "title": "Moving to the AWS Cloud",
  "description": "Moving to the AWS Cloud",
  "date": "2013-06-07",
  "url": "moving-to-the-aws-cloud/",
  "type": "post",
  "tags": [
    "aws"
  ]
}
So you want to move to the cloud. You've read about it; you like the pretty pictures. You tasted the Kool-Aid, and you know it's good (but it still feels so wrong). You carefully digest all the somewhat detailed accounts about how it can be an easy [step-by-step move to the cloud](http://www.holovaty.com/writing/aws-notes/).

It isn't easy, but it's not impossible. Adrian's notes above give you a really good skeleton, but I think there are few important points missing.  I'll run with Adrian's numbering scheme, since it seems to be a [popular reference point](http://highscalability.com/blog/2013/6/5/a-simple-6-step-transition-guide-for-moving-away-from-x-to-a.html).

I'm going to keep this limited to 6 steps, but I plan to cheat with reckless abandon.

If you haven't already, [AWS: Sign In/Up](https://console.aws.amazon.com) 

### Step 0.1: Configure IAM roles and users.

Yes, even if it's just you, and no one else is ever going to access the account, you should still configure IAM groups and users. Note that IAM roles are awesome, but as far as I'm concerned, IAM role configuration and advantages are beyond the level of step by step 'moving your system to the AWS cloud'.

Login from the main AWS management console and choose the IAM service. Create groups for Admin, Power, and ReadOnly. Admin has full access to everything, Power has full access to everything but IAM, and ReadOnly is pretty self-explanatory. Amazon provides pre-fabricated permissions policies for these groups, use the appropriate policy for each and feel free to get creative with your group names.

Create users for each group. Use real names, handles, or services and be consistent. Create keys where appropriate and ONLY use IAM keys. Putting keys into your codebase? Use IAM user keys. Configuring S3cmd with keys? Use IAM user keys. Signing up for external services that require keys? They probably tell you to create an RO account or provide a specific policy anyway! Use IAM user keys! 

Forget that your account even offers you root account level keys, just pretend that it doesn't. This makes dealing with keys and permissions much easier.

Use IAM.

One more thing within the IAM service: create an account alias from the IAM dashboard.

Before we log out of the management console and start to really distance ourselves from it, go to the EC2 service Dashboard, and select the "Key Pairs" option under Network and Security. Create a key pair with a name of your liking and save the file. You'll probably need to make sure this file is only readable to you, and you'll need it when you launch non-customized AMIs for SSH connectivity.

### Step 0.2: Log out of your root account

In line with Step 0.1, log out of the management console, and direct your browser to https://YOUR_ACCOUNT_ALIAS.signin.aws.amazon.com/console. This sets a cookie and now on the management console login page, you will see a slightly different form for authentication.

I'd strongly encourage you to spend most of your time in a read-only account, which leads me to...

### Step 0.5: Install the command line tools

It starts now, so set a good example for yourself and make everything scriptable. You should never have to login to the management console for day to day affairs or common updates (this is an outlandish goal, but I very much believe you should strive toward it). Get the command line tools to help support this goal:

*   [EC2 API Tools](http://aws.amazon.com/developertools/351)
*   [RDS Command Line Toolkit](http://aws.amazon.com/developertools/2928)
*   [Auto Scaling Command Line Tool](http://aws.amazon.com/developertools/2535)
*   [Cloudwatch Command Line Tool](http://aws.amazon.com/developertools/2534)
*   [Elastic Load Balancing Command Line Tool](http://aws.amazon.com/developertools/2536?_encoding=UTF8&jiveRedirect=1)

or, try something like this:

<pre>
mkdir /opt/aws
cd /opt/aws
wget http://s3.amazonaws.com/ec2-downloads/ec2-api-tools.zip
wget http://s3.amazonaws.com/rds-downloads/RDSCli.zip
wget http://ec2-downloads.s3.amazonaws.com/AutoScaling-2011-01-01.zip
wget http://ec2-downloads.s3.amazonaws.com/CloudWatch-2010-08-01.zip
wget http://ec2-downloads.s3.amazonaws.com/ElasticLoadBalancing.zip
</pre>

Create a file /opt/aws/.aws:
<pre>
AWSAccessKeyId=YOUR_ACCESS_KEY_ID
AWSSecretKey=YOUR_SECRET_KEY
</pre>

Those should not be root level account keys; they should be from an IAM user account with a Power user policy.

You need java. I'm not going to detail java installation or the path and environment variables here, there's good data in the links above and most of these updates are standard issue for your OS, not really specific to AWS or the cloud. There's goodOnce you have Java (I'm assuming /usr/bin/ is where 'java' lives) you may need to update this), you might find something like this helpful in your .bashrc or .profile file:

<pre>
export JAVA_HOME=/usr/bin
export AWS_HOME=/opt/aws
export AWS_RDS_HOME=$AWS_HOME/RDSCli-1.10.003
export EC2_HOME=$AWS_HOME/ec2-api-tools-1.6.5.2
export AWS_AUTO_SCALING_HOME=$AWS_HOME/AutoScaling-1.0.61.1
export AWS_CLOUDWATCH_HOME=$AWS_HOME/CloudWatch-1.0.13.4 
export AWS_ELB_HOME=$AWS_HOME/ElasticLoadBalancing-1.0.17.0
export PATH=$PATH:$AWS_RDS_HOME/bin:$EC2_HOME/bin:$AWS_AUTO_SCALING_HOME/bin:$AWS_CLOUDWATCH_HOME/bin:$AWS_ELB_HOME/bin
export AWS_CREDENTIAL_FILE=$AWS_HOME/.aws
export AWS_ACCESS_KEY=$(cat $AWS_CREDENTIAL_FILE | grep Access | awk -F= '{print $2}')
export AWS_SECRET_KEY=$(cat $AWS_CREDENTIAL_FILE | grep Secret | awk -F= '{print $2}')
</pre>

I don't know if my search-fu is slipping, but I always have to search a couple times to get to the documentation for these tools. Just in case it isn't me:

*   [EC2 Command Line Reference](http://docs.amazonwebservices.com/AWSEC2/latest/CommandLineReference/OperationList-cmd.html)
*   [EC2 Quick Reference Card (pdf)](http://awsdocs.s3.amazonaws.com/EC2/latest/ec2-qrc.pdf)
*   [Cloudwatch Command Line Reference](http://docs.amazonwebservices.com/AmazonCloudWatch/latest/DeveloperGuide/CLIReference.html)
*   [Cloudwatch Quick Reference Card (pdf)](http://awsdocs.s3.amazonaws.com/AmazonCloudWatch/latest/acw-qrc.pdf)
*   [RDS Command Line Reference](http://docs.amazonwebservices.com/AmazonRDS/latest/CommandLineReference/command-reference.html)
*   [RDS Quick Reference Card (pdf)](http://awsdocs.s3.amazonaws.com/RDS/latest/rds-qrc.pdf)
*   [Auto Scaling Command Line Reference](http://docs.amazonwebservices.com/AutoScaling/latest/APIReference/Welcome.html)
*   [Auto Scaling Quick Reference Card (pdf)](http://awsdocs.s3.amazonaws.com/AutoScaling/latest/as-qrc.pdf)
*   [Welcome.html ELB Command Line Reference](http://docs.aws.amazon.com/ElasticLoadBalancing/latest/APIReference/)
*   [ELB Quick Reference Card](https://awsdocs.s3.amazonaws.com/ElasticLoadBalancing/latest/elb-qrc.pdf)

What? You like Python better than some shell? Cool - try [boto](https://github.com/boto/boto) instead of the command line tools. It doesn't matter what tools you use, but core deployment should not depend upon intricate manual labor (which tends to be the case if you deploy strictly via the AWS Management Console).

### Step 0.6: Jump some AWS-specific hurdles

Stop thinking about iptables, and start thinking about EC2 Security Groups. Note that you can apply multiple security groups to a single instance and you can change a security group's inbound rules while it's active. More importantly note that you can NOT change the security group(s) that are associated with an active EC2 instance. Don't just use the default security group for everything. Make at least one distinct security group for each category of instance you plan to run. This allows you to open ports specifically for a particular instance. 

For example, let's say you create a security group 'web' and you apply that security group to your production web instances. Since your development web instances seem like the same category of instance, you apply security group 'web' to those as well. Now you want to implement super-cool-ninja-zombie-feature-Z, but it requires you to open ports 12345-15432 to the world (ok, unlikely scenario, but you get the point). Guess who is either going to need to do some instance/security group shuffling or open a bunch of ports unnecessarily on production?

### Step 0.8: Figure out the base for your baked AMI

I'd recommend either Amazon Linux or the newest LTS Ubuntu distro (currently 12.04.2LTS) in 64bit. These are both free AMIs to use (you pay only for the AWS instance charge) and they both do a good job of supporting some cloud-specific behavior out of the box. Amazon Linux is much lighter-weight, but the devops/cloud world seems to gravitate toward Ubuntu for some reason. Use whatever is most comfortable to you or most consistent with your current environment (hopefully those are same). Amazon Linux is a barebones CentOS/RedHat, which has merit because it has a smaller footprint; they also keep a very up to date package repository.

### Step 0.9: Choose your weapon (for baking)

I like [Puppet](https://puppetlabs.com/). There's no reason to avoid tools like [Chef](http://www.opscode.com/chef/) or [Ansible](http://ansible.cc/). If there's too much happening to dive into one of these tools, that's fine. Use a bash script, you can use raw Ruby or Python or WHATEVER! I can't endorse it, but even exporting your user history to a wiki page is better than nothing. 

Just don't rely entirely on your existing AMI. You should be able to start with a brand new base AMI and have a running version of your EC2 instance in a repeatable manner.

### Step 1: Bake an AMI

First make a security group (replace YOUR_IP with your ip). I'm allowing access from your ip over ports 22 and 80.
<pre>
SG_NAME='prod-web'
YOUR_IP='1.1.1.1'
ec2-create-group $SG_NAME -d "Production web"
ec2-authorize $SG_NAME -P tcp -s $YOUR_IP/32 -p 22
ec2-authorize $SG_NAME -P tcp -s $YOUR_IP/32 -p 80
</pre>

For your actual security groups you should generally replace the "-s IP/32" with "-o some-security-group". This allows you to limit access to your web servers to just the load balancer, or to limit access to a shared back-end server to just instances with the 'prod-web' security group. 

Then launch an EC2 instance that uses the security group, note that I'm using the $SG_NAME variable from above and I'm storing part of the response data in the $INSTANCE_ID variable.

#### Amazon Linux 2013.03.1:

<pre>
INSTANCE_ID=$(ec2-run-instances ami-05355a6c -n 1 -g $SG_NAME -t t1.micro -z us-east-1e -k YOUR_KEYPAIR_NAME | grep INSTANCE | awk '{print $2}')
</pre>

#### Ubuntu 12.04.2LTS:

<pre>
INSTANCE_ID=$(ec2-run-instances ami-d0f89fb9 -n 1 -g $SG_NAME -t t1.micro -z us-east-1e -k YOUR_KEYPAIR_NAME | grep INSTANCE | awk '{print $2}')
</pre>

Tip: Tags (and userdata) can be really useful. Also, you can always access [metadata for the current instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AESDG-chapter-instancedata.html) from said instance.

<pre>
ec2-create-tags $INSTANCE_ID --tag "Name=my-web"
</pre>

Once this instance becomes available, connect via ssh using your keypair. "ec2-describe-instances" will show you the status of your instance(s) and also the public dns value that you can use to connect. You can also use any account with read access to ec2 to view status details if you want to stick with the management console. The default Ubuntu user is "ubuntu", while the default Amazon Linux user is "ec2-user".

<pre>
ec2-describe-instances
ssh -i /path/to/keypair user@publicDNS
</pre>

Add your code base and don't forget things like:

*   Create your own users (and put your public keys on the image)
*   Disable password based authentication for ssh (if enabled)
*   Remove or reduce the privileges of the default user for your AMI
*   You might need to mount EBS volumes (though seriously consider updating your tasks to go directly to S3)
*   Configure your services to start automatically

Once you're satisfied with the state of your instance, create an AMI based on it. If you still have a populated $INSTANCE_ID variable, we can use that to identify the instance for AMI generation. If not, we can use the Name tag to identify it easily (I'm using the hardcoded name tag from above. Let's go ahead and add a name tag to the new AMI, too.

<pre>
$INSTANCE_ID=$(ec2-describe-instances -F tag:Name=my-web | grep INSTANCE | awk '{print $2}')
$IMAGE_ID=$(ec2-create-image $INSTANCE_ID --name my-web-ami-YYYYmmdd | grep IMAGE | awk '{print $2}')
ec2-create-tags $IMAGE_ID --tag "Name=my-web"
</pre>

### Step 1.2: Only keep necessary security group rules

For day to day affairs, you are unlikely to need ssh access to your now-ephemeral web instances. Just remove port 22 access entirely from the security group. You can always re-authorize it (using the command above) if you need to access a current instance. I usually use the default security group for 'baking' and it keeps 22 open to most requests, since I know that the instance will generally only be up and running while I'm connected to it.

### Step 1.5: Elastic Load Balancers

You need to put an Elastic Load Balancer in front of your instances, this allows the auto scaling group to add new instances when necessary and actually let users take advantage of them. Note that if you like your site to serve from example.com, instead of www.example.com, you'll also want to get a start on moving to Route53 for DNS (not a bad idea, but DNS changes require their own orchestration). 

I'm going to call our Elastic Load Balancer "webElb" and I'll use the $ELB variable to reference it. I'm going to configure it for both HTTP and HTTPS. I'm also going to configure a health check that loads a specific page, you should have something that's easy to process, but actually determines if your instance is unhealthy.

You probably want to have a 'stickiness' setting. Users should return to the same instance if they are coming back in a reasonable amount of time.

<pre>
ELB=webElb
elb-create-lb $ELB -l "protocol=HTTP,lb-port=80,instance-port=80" -z us-east-1e
elb-configure-healthcheck $ELB --target "HTTP:80/somePath/health/check" --interval 60 --timeout 4 --unhealthy-threshold 2 --healthy-threshold 2
</pre>

Likely next steps on the ELB front are adding SSL listeners (the load balancer can handle the SSL handshake and then pass HTTP requests back to your web instances) and setting stickiness (serve requests from the same person via the same instance).

### Step 2: Launch Configurations, Auto Scaling Groups, and Cloud Watch Monitors, Oh my!

This looks worse than it is. Make a launch config with some basic details about the type of instances that you want to launch within your auto scaling group. Create an auto scaling group and tell it where to live, what kind of instance limitations it should have, load balancer association, and the launch configuration to use. Create scaling policies and monitors to trigger those policies.

<pre>
ASG=my-web-asg
LC=web-lc-YYYYmmdd
as-create-launch-config $LC --image-id $IMAGE_ID --instance-type t1.micro --group "securitygroup" --user-data "web,someTag"
as-create-auto-scaling-group $ASG --availability-zones us-east-1e --min-size 1 --max-size 12 --load-balancers $ELB --launch-configuration $LC --tag "k=Name,v=my-web,p=true" --tag "k=GitTag,v=someTag,p=true"
UP=$(as-put-scaling-policy scaleUp --auto-scaling-group $ASG --adjustment=1 --cooldown 60 --type ChangeInCapacity)
DOWN=$(as-put-scaling-policy scaleDown --auto-scaling-group $ASG --adjustment=-1 --cooldown 120 --type ChangeInCapacity)
mon-put-metric-alarm cpuMax  --comparison-operator  GreaterThanThreshold  --evaluation-periods  1 --metric-name  CPUUtilization  --namespace  "AWS/EC2"  --period  300  --statistic Average --threshold 80 --dimensions "AutoScalingGroupName=$ASG" --alarm-actions $UP
mon-put-metric-alarm cpuMin  --comparison-operator  LessThanThreshold --evaluation-periods  1 --metric-name  CPUUtilization --namespace  "AWS/EC2"  --period  600  --statistic Average --threshold 20  --dimensions "AutoScalingGroupName=$ASG" --alarm-actions $DOWN
</pre>

### Step 3: Sessions and shared Cache

I'm not going to go into great detail here, because this isn't really a cloud-specific issue. There are some cloud features/constraints that should definitely impact your decision, but caching can be tricky and it's worth spending time on how you plan to implement it.

Adrian's solution isn't a bad one, and I'm sure it meets the needs of many. My personal preference would be to spin up a separate cluster (memcached or redis) for handling these; or use the previously mentioned AWS ElastiCache (maybe even SimpleDB). This depends on a lot on what's easiest to implement. As Adrian pointed out, for many developers/orgs who are using frameworks, the move to cookie based sessions is likely to be quite simple. Simple is good.

### Step 4: Migrate to MySQL

I side comfortably with the faction of PostgreSQL users that get a little concerned when there's a migration toward MySQL. Nonetheless, RDS (and MySQL) is perfectly decent. This is another thing that's really not cloud-specific, it just happens to be the case that Amazon built their RDS service with support for MySQL.

Note that RDS feels a little expensive at first; avoid the temptation to compare RDS pricing to a comparable pair of EC2 instances plus some EBS volumes. Just switch to RDS and don't look back. If you genuinely get to a point where you are outgrowing RDS, well done.

### Step 5: Deployment

This is another area that requires some project or org-specific decisions. [Fabric](http://docs.fabfile.org/en/1.6/) is recommended in the original Heroku to AWS article. [Capistrano](http://capistranorb.com/) is another good option, as is [func](https://fedorahosted.org/func/). Depending on your source control setup and familiarity, a couple git hooks might work well. If you are working with a relatively small number of servers at any given time, a simple looping shell script might work well. Like puppet? Check out the [Marionette Collective](http://docs.puppetlabs.com/mcollective/index.html). Both Chef and Puppet will also let you run agents that periodically check for updates. My personal preference is to maintain code state elsewhere, but there's no reason you couldn't use these agents to keep everything in sync (or as a tool in the chain).

### Step 5.1: Deployment (Auto Scaling)

Remember that if you are using a pre-baked AMI, and you elect to support code deployment via some deployment tool, your pre-baked AMIs are going to be added via Auto Scaling and will be running old code. 

This is yet another point that depends heavily on the needs of your project. If you can get away with it, follow the Netflix technique of using an AMI as the smallest increment of deployment. If you need/want to be able to deploy code in a more real-time fashion on running instances, you need to bake something into your AMI to make sure that your instance states are consistent.

A launch time task that pulls master or runs checkout on a particular tag might work. You can even put the current tag in an EC2 tag or the instance userdata such that AutoScale automatically adds it. A periodic task that checks for the current code is another option (more of an automated deployment); this is a little scary for some, but would probably help keep master in a stable state.

### Step 5.2: Deployment (Don't do it alone)

Remember how we talked about making everything scriptable? If you have all these scripts ready to run, why not have someone else deploy for you. Watch this tale of [automation and hubot](https://www.youtube.com/watch?v=DH2twW0dmrM) at Github. Really, watch it. Now sign up for [HipChat](https://www.hipchat.com/) (free for small groups), and build yourself a bot to handle everything you need. I like [wobot](https://github.com/cjoudrey/wobot) (the [node.js aws sdk](https://aws.amazon.com/sdkfornodejs/) is coming along nicely), [hubot](https://github.com/github/hubot) is great, and there are [a few others](http://help.hipchat.com/knowledgebase/articles/64359-running-a-hipchat-bot).

### Step 6: Update your AMI

So you needed a new AMI. All you need to do is create a new launch configuration (featuring the new AMI id), and apply the new launch configuration to your auto scaling group. 

<pre>
NEW_LC=web-lc-YYYYmmdd
as-create-launch-config $LC --image-id $NEW_IMAGE_ID --instance-type t1.micro --group "securitygroup" --user-data "web,someNewerTag"
as-update-auto-scaling-group $ASG --launch-configuration $NEW_LC
</pre>

New instances that enter your auto scaling group will now be based on the $NEW_IMAGE_ID ami.

### Welcome to the Cloud

You made it; welcome to the cloud - and all in just six easy steps.

Remember to use consistent names for everything (not just IAM). Name instances consistently, AMIs, ELB, ASG, RDS. Anything AWS provides that you can abbreviate should be named consistently. Use tags, userdata, and metadata relentlessly. Tags are a great way to handle rollbacks when they need to happen

Think there should be an easier way? [Netflix OSS](http://netflix.github.io/#repo) offers a lot of tools to automate these procedures. [Asgard](https://github.com/Netflix/asgard) is a good place to start; I even made a few [Asgard public AMIs](http://imperialwicket.com/tags/asgard) with the whole package pre-installed for you. 

If you made it this far, congratulations. Your next challenge is to look over the [AWS architecture OFA used](http://awsofa.info/) until it makes sense. Work on Multi-AZ deployments and get [monkey-proof](https://github.com/Netflix/SimianArmy) (beating the chaos monkey is a nice challenge). Happy cloud-wrangling.
