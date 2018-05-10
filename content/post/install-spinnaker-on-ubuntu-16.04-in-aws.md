{
  "title": "Install Spinnaker on Ubuntu 16.04LTS in AWS",
  "description": "Install Spinnaker on Ubuntu 16.04LTS in AWS",
  "date": "2018-05-09",
  "url": "install-spinnaker-on-ubuntu-16.04-in-aws/",
  "type": "post",
  "tags": [AWS, spinnaker],
  "draft": false
}
# Install Spinnaker on Ubuntu 16.04LTS in AWS

I encountered a lot of issues installing [Spinnaker](https://www.spinnaker.io/) (1.6.1 and 1.7.x) on Ubuntu in Amazon Web Services. This post is the collection of steps I took to get a demo server with a working Spinnaker and Jenkins installation. I am going to use [GitHub](https://github.com/) and the [GitHub Branch Source Plugin](https://go.cloudbees.com/docs/cloudbees-documentation/cje-user-guide/index.html#github-branch-source) from CloudBees in order to trigger my build/bakes. My target flow for the demo is:

1. Push a branch/create a pull request with changes (human action)
2. Jenkins runs a pipeline via Jenkinsfile to test updates and certify merge
3. Merge the pull request to master (human action)
4. Jenkins runs a pipeline via Jenkinsfile to test updates and builds a Debian package
6. Spinnaker runs a simple Bake and Deploy (red/black) pipeline

This implementation is opinionated and very insecure, but it gives you an easy 10-15 minute commit through deploy demonstration to really open discussions about Spinnaker as a deployment platform for your project or organization. 


### This demo requires:

* AWS account - I recommend a clean account for this demo. If you are using an existing account try to run things in an alternative region or a unique vpc. A reasonable server is necessary to run Spinnaker and Jenkins. It will likely cost around $100 per month if you leave it and the demo server running 24/7. The aws cli tools are helpful, but I will reference the AWS mgmt console.
* GitHub account
* Git repository that includes test script(s) and a build script that results in a Debian package.
* Some DNS control to point a CNAME at your EC2 instance (or familiarity to manage it with hosts files).


### What this is NOT:
* This is NOT Production appropriate - I skip certificates, authentication, and authorization. I also skip load balancing and barely touch DNS. Do NOT use this in production.
* This is NOT FREE - This will cost money. Not a lot, but more than your average hobby server.
* This is NOT a container/Kubernetes demo. [Those exist](https://www.spinnaker.io/setup/install/providers/kubernetes/) and k8s is great, but for organizations who are not already using containers they simply complicate matters.


## [spinnaker-demo](https://github.com/imperialwicket/spinnaker-demo)

[Spinnaker-demo](https://github.com/imperialwicket/spinnaker-demo) is a sample repo that includes a Jenkinsfile (with some master branch specific work), includes a few simple test files, and builds a Debian package that is designed to work on a vanilla Ubuntu server. Spinnaker-demo is really two html files and a bunch of test and packaging logic. This makes it easy for any developers or interested parties to make simple changes to an html file and watch their changes go live.

You can clone this repo or generate your own, but I think it is important that the demo application be simple so that version changes are apparent and updates are not a source of complication.


## AWS account preparation

We need to create a few AWS objects the Spinnaker needs and/or expects in order to function properly. For simplicity and consistency, this Spinnaker instance will run and deploy in us-west-2. This is mostly a reworking of the [setup instructions](https://www.spinnaker.io/setup/install/providers/aws/) with a few additions and some subtle naming changes.


### Create an IAM PowerUser

This is more than Spinnaker requires, but the goal is to get Spinnaker up and running with the least hassle.

Login to AWS and proceed to [add a new IAM user](https://console.aws.amazon.com/iam/home#/users$new).

1. Create a user named 'spinnaker' with access type Programmatic access.
1. On the set permissions screen, create a group 'powerusers' with the PowerUserAccess policy. We'll use this to establish IAM configuration. Spinnaker will run under a different (non-Admin - but still powerful) user.
1. Review the user and create.
1. Download the access key id and secret access key, we will reference these as $spinnaker_access_key_id and $spinnaker_secret_access_key. 


### Add a VPC and Subnet

1. Go to the [VPC Wizard](https://us-west-2.console.aws.amazon.com/vpc/home?region=us-west-2#wizardSelector:)
1. Select "VPC with a Single Public Subnet"
1. Enter VPC name 'spin-demo'
1. Use Availability Zone 'us-west-2a'
1. Enter Subnet name 'spin-demo.internal.us-west-2a'
1. Create VPC
    ![Create VPC](/files/spinnaker-create-vpc.png)
1. Add a Tag to your subnet indicating the purpose with Key:"immutable_metadata" and Value:"{"purpose": "spin demo"}
    ![Subnet Tags](/files/spinnaker-subnet-tags.png)


### Create an IAM EC2 Role

When Spinnaker launches an EC2 instance, it will assign this role. For a light demonstration this role may not be important, and may not even get used, but Spinnaker expects to have a Role available for assignment.

1. Go to the [IAM Create Role](https://console.aws.amazon.com/iam/home?region=us-west-2#/roles$new) page
1. Use the default "Aws service" as type of trusted entity
1. Select "EC2" as the service that will use this role
1. Do not attach any policies and proceed to the review
1. Enter Role name 'spinnaker-launched'
1. Create the role.


### Create an EC2 KeyPair

When Spinnaker launches an EC2 instance, it will associate this key pair. Like the EC2 Role, this may not be necessary for a demonstration, but Spinnaker will expect to assign a key pair to instances it launches.

1. Go to [EC2 Key Pairs](https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2#KeyPairs)
1. Use the "Create Key Pair button to create a new key pair
1. Enter key pair name 'spinnaker-launched' and create a key pair
1. Store this appropriately for use


### Create IAM Policies and the EC2 Role for the Spinnaker instance

For whatever reason, the concept of "managing/managed" feels overly complicated to me. I am using a single account, and I'll name with the concept of primary and assumed. The important parts here are our two EC2 roles: spinnaker (itself/primary) and spinnaker-assumed. Spinnaker needs a policy that allows the AssumeRole action, so that it can assume the spinnaker-assumed role and launch ec2 instances there. Spinnaker-assumed needs a policy that allows the PassRole action, so that it can pass the spinnaker-launched IAM EC2 role to newly launched instances. In addition, spinnaker-assumed needs to have a trust relationship with spinnaker in order to allow it to use AssumeRole successfully.

#### Create the AssumeRole policy

1. Go to [IAM Create Policy](https://console.aws.amazon.com/iam/home?region=us-west-2#/policies$new)
1. Replacing <YOUR_ACCOUNT_ID> with your AWS account id, select the JSON tab and enter:
    ````
    {
        "Version": "2012-10-17",
        "Statement": [{
            "Action": "sts:AssumeRole",
            "Resource": [
                "arn:aws:iam::<YOUR_ACCOUNT_ID>:role/spinnaker-assumed"
            ],
            "Effect": "Allow"
        }]
    }
    ````
1. Review the policy and provide the name 'spinnakerAssumeRolePolicy'
1. Create the policy

#### Assign the spinnakerAssumeRolePolicy to the spinnaker IAM user

1. Go to the [IAM spinnaker user](https://console.aws.amazon.com/iam/home?region=us-west-2#/users/spinnaker)
1. Select the "Add permissions" button
1. Use the "Attach existing policies directly" option and filter to the spinnakerAssumeRolePolicy
1. Attach the spinnakerAssumeRolePolicy, Review, and Add

#### Create the PassRole policy

1. Go to [IAM Create Policy](https://console.aws.amazon.com/iam/home?region=us-west-2#/policies$new)
1. Replacing <YOUR_ACCOUNT_ID> with your AWS account id, select the JSON tab and enter:
    ````
    {
        "Version": "2012-10-17",
        "Statement": [{
            "Effect": "Allow",
            "Action": [ "ec2:*" ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::<YOUR_ACCOUNT_ID>:role/spinnaker-launched"
        }]
    }
    ````
1. Review the policy and provide the name 'spinnakerPassRolePolicy'
1. Create the policy

#### Create the spinnaker-assumed role

1. Go to the [IAM Create Role](https://console.aws.amazon.com/iam/home?region=us-west-2#/roles$new) page
1. Use the default "Aws service" as type of trusted entity
1. Select "EC2" as the service that will use this role
1. Attach permissions policies PowerUserAccess and spinnakerPassRolePolicy
1. Review the role and enter Role name 'spinnaker-assumed'
1. Create the role

#### Establish Trust relationship

The spinnaker-assumed role must 'trust' the spinnaker user. 

1. Go to the [spinnaker IAM user] and copy the user ARN
1. Go to the [Trust relationships tab for the spinnaker-assumed role]
1. Edit trust relationship and change it to (replaceing spinnaker-user-arn with your arn):
    ````
    {
        "Version": "2012-10-17",
        "Statement": [{
            "Sid": "1",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<spinnaker-user-arn>"
            },
        "Action": "sts:AssumeRole"
        }]
    }
    ````
1. Update the Trust relationship


## Launch an EC2 instance

I had success with an r4.large; an m5.xlarge or maybe m5.large should also work well. In the interest of not troubleshooting memory issues I would use something with at least 8GB of RAM.

1. Go to the aws [EC2 instance launch wizard](https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2#LaunchInstanceWizard:)
1. Launch an Ubuntu Server 16.04 LTS (HVM) instance (ami-4e79ed36 at time of writing)
1. Choose instance type m5.xlarge (or your desired alternative)
1. Launch into the spin-demo configured vpc/subnet (Not necessary, but simplifies things)
1. Change Auto-assign Public IP to "Enable"
1. For a demonstration an 8GB volume is probably sufficient, but bumping up a little gives you some room for logs and other storage without causing concern. 
1. Add Tags as desired (Name tags are convenient if you're using the AWS mgmt console)
1. Create a new Security Group named 'spinnaker'
1. Open ports 22, 8084, 8888, 9000 to your IP (or the IPs you want to have access)
1. Open port 8888 to the [CIDR ranges for GitHub's hooks](https://api.github.com/meta)
1. Launch the instance, creating a new key pair or using a functional key pair.

![EC2 instance review and launch](/files/spinnaker-ec2-launch.png)

Once the instance is up, use your desired DNS solution to point some domain at the public IP of the EC2 instance. Be sure to remove this DNS record when you remove the demo server!

## Install Jenkins

Connect to your server via ssh and install jenkins and some "build server" dependencies. I am installing from jenkins-ci.org and also installing the dependencies to support an s3 apt repository (deb-s3). I am moving Jenkins to port 8888 because Spinnaker and Jenkins will both try to use 8080.

````
wget -q -O - https://jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install openjdk-8-jdk jenkins software-properties-common
sudo sed -i 's/=8080/=8888/g' /etc/default/jenkins
sudo service jenkins restart
sudo apt-add-repository ppa:brightbox/ruby-ng
sudo apt-get update
sudo apt-get install build-essential ruby2.2 ruby2.2-dev zlib1g-dev liblzma-dev
sudo gem install bundler dev-s3
 sudo cat /var/lib/jenkins/secrets/initialAdminPassword
````

## Configure Jenkins

I am installing a completely generic Jenkins and I am going to leave it protected only by the security group. Again, this is NOT suitable for production.

1. Navigate to `http://<spinnaker-instance-uri>:8888` and enter the initialAdminPassword we retreived from the server.
1. Choose "Install suggested plugins"
1. Use the "Continue as admin" link to skip user generation
1. Save and Finish
1. Go to Configure Global Security and uncheck the "Enable security" option (again, demonstrative purposes only)
1. Under Global Security, uncheck "Prevent Cross Site Request Forgery exploits"
1. Go to Manage Plugins and install the Blue Ocean Plugin and its dependencies, restart Jenkins after installation.

## Connect demo repo and Jenkins

### The spinnaker-demo repo (or a suitable alternative)

The [spinnaker-demo](https://github.com/imperialwicket/spinnaker-demo) project is setup to be fairly generic and functional for simple pipeline demos or to get to a working starting point. Clone that or create a comparable repository to link with Jenkins (via GitHub Branch Source Plugin).

### GitHub Access Token and the Jenkins GitHub Organization Job

Once you have a repository, proceed to your [GitHub access tokens](https://github.com/settings/tokens) and generate a new token for spinnaker-demo with admin:repo_hook, read:org, and repo:status scopes. Copy that token, and use it to configure your organization in Jenkins.

1. Go to Jenkins and create a New Job
1. Select GitHub Organiation
1. Enter an item name `github-<username>` (or whatever you prefer)
1. In GitHub Organization, choose "Add" -> `github-<username>` to add your token for this job.
1. Enter Password `<the-github-access-token>` and leave all other fields blank.
1. Add the credential and then choose the `/********` credentials option
1. Ensure the Owner field is accurate (it defaults to the job name)
1. To reduce the scan overhead, Add the optional GitHub Organization Behavior "Filter by name (with wildcards)" and only include 'spinnaker-demo' (or your desired repository).
1. I like to periodically check the organization in GitHub every hour instead of the default once per day
1. Save the job config.

![GitHub Organization Config](/files/gh-org-config.png)

The scan should succeed and likely kick off a master branch test that will fail as it has no changes.

Proceed to the repository settings/hooks page and add a Webhook pointing at `http://<your uri>:8888/github-webhook/`. All the other options can remain at their default.

### GitHub master branch protection

In order to highlight an enforced control, enable master branch protection and require a successful check before merging to master. If you are using an organization or have multiple collaborators, consider requiring a review as well.

![GitHub Branch Protection](/files/gh-branch-protection.png)

### S3 bucket and user for our apt repository

1. Go to [IAM add user](https://console.aws.amazon.com/iam/home?region=us-west-2#/users$new)
1. Enter User name 'jenkins-apt' and check "Programmatic access".
1. Attach existing policy "AmazonS3FullAccess" (again: over provisioned for simplicity).
1. Create user
1. Store the access key id and secret access key for adding to Jenkins in a moment
1. Create a bucket in us-west-2 (Oregon) with Static Website Hosting enabled (Index Document: index.html) and otherwise default options. Note the bucket name for later.

Jenkins will kick of the job that builds our Debian package and attempt to use deb-s3 to store it in an s3 bucket. We must add the bucket name, access key id, and secret access key values to jenkins as Global or domain scoped credentials. I am using Global, because (one more time) this is not suitable for production use. Create 3 Global Credentials in Jenkins of type "Secret text". Jenkinsfile expects credential ids: jenkins-aws-s3-spinnaker-demo-bucket, jenkins-aws-s3-spinnaker-demo-access-key-id, and jenkins-aws-s3-spinnaker-demo-secret-access-key to be available. Set those accurately, and the deb-s3 upload should successfully post our Debian packages.

![Jenkins AWS Credentials](/files/jenkins-aws-creds.png)

At this point, you can checkout a non-master branch, make an update, push the branch to GitHub, and create a PR to validate a few demonstrative concepts:

1. The tests are practically meaningless
1. Environment variable usage in the Jenkinsfile
1. Parallel and serial step execution in adjacent stages
1. Branch name conditions controlling stages (only build from master - which helps our branch protection gate our artifact generation)
1. GitHub Pull Requests show the required branch test

![Jenkins non-master pipeline](/files/jenkins-non-master.png)
![GitHub Pull Request Checks](/files/gh-pr-check.png)

 
## Install Halyard

Halyard manages configuration of Spinnaker, and while you can configure things manually or try to use configuration management I've only encountered frustration when trying to do Halyard's work in another way. My implementations use Halyard and then throw a backup somewhere that we can fetch on launch. It's not classy, but it works.

I will update the hosts file here, you'll need to set YOUR_CONFIGURED_URI (from the DNS update above)

````
export YOUR_CONFIGURED_URI=<your uri>
sudo sed -i "s/localhost/localhost $(hostname) $YOUR_CONFIGURED_URI/" /etc/hosts
curl -O https://raw.githubusercontent.com/spinnaker/halyard/master/install/debian/InstallHalyard.sh
chmod 0750 InstallHalyard.sh
sudo ./InstallHalyard.sh
````

My Halyard version installed at 1.1.0.


## Configure and Launch Spinnaker

Use `hal version list` to show available spinnaker releases and Halyard version compatibility. I will deploy spinnaker at version 1.7.3. I am setting explicit access key id and secret access key values for AWS services instead of using an EC2 instance Role. I experienced what seemed like front50 and/or clouddriver issues when trying to rely on an instance role and have not had time to further troubleshoot. Explicitly setting the keys is either more reliable or a happy coincidence with other updates I made.

I found the [Halyard command reference](https://www.spinnaker.io/reference/halyard/commands/) helpful when dealing with these configurations.

One more time, these commands include a few concerning implementations from a security perspective, and this configuration does not include any authentication/authorization mechanisms. Do NOT open the spinnaker security group to unknown IPs or users; Spinnaker has poweruser access to your AWS account.

````
export SPINNAKER_ACCESS_KEY_ID=<spinnaker user access key id>
 export SPINNAKER_SECRET_ACCESS_KEY=<spinnaker user secret access key>
export SPINNAKER_AWS_ACCOUNT_DISPLAY_NAME=<what you want your account to be called in spinnaker, e.g. 'my-aws-account'>
export SPINNAKER_AWS_ACCOUNT_ID=<your aws account id>
export SPINNAKER_URI=<your spinnaker uri>

hal config deploy edit --type localdebian
hal config version edit --version 1.7.3
echo $SPINNAKER_SECRET_ACCESS_KEY | hal config storage s3 edit --region us-west-2 --access-key-id $SPINNAKER_ACCESS_KEY_ID --secret-access-key
hal config storage edit --type s3
echo $SPINNAKER_SECRET_ACCESS_KEY | hal config provider aws edit --access-key-id $SPINNAKER_ACCESS_KEY_ID --secret-access-key
hal config provider aws account add $SPINNAKER_AWS_ACCOUNT_DISPLAY_NAME --account-id $SPINNAKER_AWS_ACCOUNT_ID --regions us-west-2 --assume-role role/spinnaker-assumed
hal config provider aws enable
echo "host: 0.0.0.0" | tee     ~/.hal/default/service-settings/gate.yml     ~/.hal/default/service-settings/deck.yml
hal config security ui edit --override-base-url http://$SPINNAKER_URI:9000
hal config security api edit --override-base-url http://$SPINNAKER_URI:8084
hal config ci jenkins master add local-jenkins --address http://$SPINNAKER_URI:8888
hal config ci jenkins enable

sudo hal deploy apply
````

On the first launch you can not flush the infrastructure caches, but if you are making updates and restarting spinnaker, I highly recommend using `sudo hal deploy apply --flush-infrastructure-caches` to be sure your cached data is not causing confusion. After launch, give the various services time to start. Nothing should be using much CPU, and you should be able to confirm that ports 9000, 8084, and 8888 are not bound to 127.0.0.1 (`netstat -lnt`). Spinnaker logs all route to /var/log/syslog on 16.04 LTS with Spinnaker 1.7.3 by default, hopefully there's not a lot to troubleshoot, but that's a good place to start if need be.

Fire up http://$SPINNAKER_URI:9000 and bask in all its DevOpsy radiance.


## Create an Application and Deploy Pipeline

I create an application with an ELB and a Security Group, and also a deploy pipeline. Our pipeline will be a simple Bake + Deploy, that is triggered by a successful test on our repository's master branch (which should be the only time a deploy artifact is created). 

### Create an Application

1. Use the "Actions" drop down on the Search or Applications tab to choose "Create Application"
1. Enter Name "demo"
1. Enter Owner Email `<some email>`
1. Enter Repo Type "github"
1. Enter Repo Project "spinnaker-demo"
1. Enter Repo Name "spinnaker-demo"
1. Check "Consider only cloud provider health when executing tasks"
1. Create the Application

![Spinnaker Create Application](/files/spin-create-app.png)

### Create a Security Groups

Create two security groups: one for the ELB and one for the EC2 instances.

Note that Spinnaker expects security groups to be present in AWS, even though there is a create Security Group button. Without security groups present in your vpc, the UI will throw errors.

1. Go to [Security Groups](https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2#SecurityGroups:sort=groupId) in AWS management console
1. Create a new security group
1. Enter Security group name "demo-elb"
1. Enter Description "demo-elb"
1. Choose the demo vpc
1. Add Rule HTTP (80) from your IP (or anywhere if you want the demo app to be wide open), and create
1. Create a new security group
1. Enter Security group name "demo"
1. Enter Description "demo"
1. Choose the demo vpc
1. Add Rule HTTP (80) from the demo-elb security group, and create.

### Create a Load Balancer

1. From the "demo" Application, select Infrastructure -> Load Balancers
1. Use the plus button to add a Load Balancer
1. Select "Classic (Legacy)" to create an ELB (it's easier), and select "Configure Load Balancer"
1. Choose your AWS account and the us-west-2 region.
1. Enter Stack "web"
1. Enter vpc subnet "demo"
1. Choose Security Group "demo-elb"
1. Enter Listener details External `HTTP 80` and Internal `HTTP 80`
1. Enter Health Check `HTTP 80 /health` (assuming you are using the spinnaker-demo repository)
1. Create your load balancer

![Create ELB 1](/files/spinnaker-create-elb.png)

![Create ELB 2](/files/spinnaker-create-elb2.png)


### Create a Spinnaker Pipeline

I will create a simple Bake and Deploy pipeline, and we'll use some custom parameters a trigger our pipeline based on the spinnaker-demo master branch test.

1. Go to the demo application and choose the Pipelines tab
1. Select "Configure a new pipeline"
1. Enter name "demo-web-deploy"
1. Under Automated Triggers, select Add Trigger
1. Select Type "Jenkins"
1. Choose the "local-jenkins" master
1. Choose the "github-*/job/spinnaker-demo/job/master" job (or whatever your repo name is)
1. In the top pipeline display, select Add Stage and choose type Bake
1. Under Bake Configuration, enter Package name "spinnaker-demo"
1. Under Bake Configuration, check Show Advanced Options
1. Add Extended Attribute: 
    1. Key=`aws_subnet_id`
    1. Value=`subnet-<id for demo.internal.us-west-2a subnet>`
1. Add Extended Attribute:
    1. Key=`repository`
    1. Value=`http://<your apt repo bucket>.s3-website-us-west-2.amazonaws.com trusty main`
1. In the top pipeline display, select Add Staging and choose type Deploy
1. Under Deploy Configuration, Select "Add server group"
1. Use pre-populated Account, Region, and VPC Subnet
1. Enter Stack "web"
1. Use Strategy "Red/Black
1. Check "Rollback to previous server group if deployment fails"
1. Under Load Balancers, choose the Classic Load Balancer "demo-web"
1. Under Security Groups, choose the Security Group "demo"
1. Under Instance Type, choose Micro Utility -> Micro (Nano would probably work, too)
1. Leave Capacity->Number of Instances at 1
1. Leave Availability Zones as is
1. Under Advanced Settings, choose Key Name "spinnaker-launched"
1. Under Advanced Settings, enter IAM Instance Profile "spinnaker-launched"
1. Add the Cluster

![Spinnaker Pipeline Bake](/files/spinnaker-pipeline-bake.png)

![Spinnaker Pipeline Deploy](/files/spinnaker-pipeline-deploy.png)


## Pull Request Merge and Deploy

1. Go back to the GitHub Pull Request and Merge to master via GitHub UI
1. Assuming the master test passed Spinnaker should trigger the demo-web-deploy pipeline that will try to bake your AMI and deploy your first cluster.

![Spinnaker Server Group Actions](/files/spinnaker-rollback-destroy.png)


## Next steps and troubleshooting

Deploy a few revisions, reconfigure, and demonstrate the Rollback/Enable/Disable features within the Red/Black deploy. A demo like this was a key first step to gaining support for a more fully featured proof of concept.

I tried to be overly opinionated in the interest of getting a functional demo in a reliable way. If you are seeing issues check security groups, hostnames, CORS, and caching. If nothing obvious shows up, check the [Spinnakerteam Slack](https://spinnakerteam.slack.com/), lots of helpful people are usually around.


## Additional resources

[Spinnaker setup](https://www.spinnaker.io/setup/install/)
[Spinnaker AWS](https://www.spinnaker.io/setup/install/providers/aws/)
[Spinnaker Hello Deployment Codelab](https://www.spinnaker.io/guides/tutorials/codelabs/hello-deployment/)
[Armory Application Deployment Pipeline](https://docs.armory.io/install-guide/application_pipeline/)
[Armory Install Guid](https://docs.armory.io/install-guide/)
[StackOverflow - Spinnaker VPC naming](https://stackoverflow.com/questions/43393804/no-account-vpc-available-when-trying-to-create-security-group)
[Spinnakerteam Slack](https://spinnakerteam.slack.com/)
