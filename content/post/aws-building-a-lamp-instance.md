{
  "title": "AWS: Building a LAMP instance on Amazon Linux",
  "description": "AWS: Building a LAMP instance on Amazon Linux",
  "date": "2011-12-14",
  "url": "aws-building-a-lamp-instance/",
  "type": "post",
  "tags": [
    "aws"
  ]
}
_[UPDATE] Since I reference this occasionally, and it gets a fair amount activity, I thought I would make a more rudimentary version for anyone wanting easy access to a quick lamp setup on Amazon Linux. You can find the updated version at [AWS: Quick and secure LAMP on Amazon Linux](http://imperialwicket.com/aws-quick-and-secure-lamp-on-amazon-linux). It's a little more in depth of a setup, and there is very little explanation. 

If you are new to Linux/AWS I recommend you read this first, and head over to the Quick and secure setup after._

So you created an Amazon Web Services account when they announced that you could get services for free.  Now what?  The most direct transition to the cloud from your current implementation (or a good way to get started for anyone who doesn't have a virtual dedicated server) is to load an EC2 instance and configure it as a standalone LAMP stack.  This puts definite ceilings on your scalability, and fails to take advantage of a number of the features available from AWS, but it remains a very practical place to start.  

I am assuming that your AWS account is created, and you have already entered a credit card number for billing purposes.  I'll try to avoid creating anything that will definitely result in fees, or mention it explicitly if I do something that might require them.  Nonetheless, I'm not responsible if Amazon bills you for something - Whether it was in my instructions, or not.

Login to your AWS account.  The default entry point is the S3 tab.  On the S3 tab, create a bucket with a meaningful, unique name.  We won't use this now, but it will be nice to have later.  

Switch to the EC2 tab, where there is a prominent "Launch Instance" button, select it.  The easiest way to start an instance is by using a Quick Start Amazon Machine Image (AMI).  I recommend the Basic 64-bit Amazon Linux AMI 1.0 (ami-2272864b).  This is a bare-bones CentOS-based Linux system.  It comes backed with an Elastic Block Store (EBS) disk volume for data persistence (EC2 instances alone are non-persistent - if you shut it down, it's gone).  The EBS volume also makes custom AMI generation and server snapshots quite simple using the AWS Management Console.  After selecting your AMI (again, I will reference the Basic 64-bit Amazon Linux AMI with Id: ami-2272864b), you must choose the details for our instance.  Instance details denote the type of instance, number of instances, and location of your instance.  For now, we'll ignore spot instances and Private Cloud.  The number of instances and the Availability zone are fine using defaults.  Note that the default Instance Type is (at time of writing) "Large (m1.large, 7.5 GB)".  The Large instance is not a bad server - but it's also not free, switch to Instance Type "Micro (t1.micro, 613 MB)".  The micro instance is a pretty weak server, but it's fine for small sites and more than adequate for investigating AWS.  Continue to the additional instance options.  When your site goes viral, and you are scaling, these might become important.  For most implementations, the defaults are adequate. Enable monitoring if you like (it's a nice system), but it costs money.  You can leave User Data blank.  Continue to the Key Value tags for your instance.  Give your instance a tag with key "Name", I used value "Free Usage Instance 101", this helps for identification in the AWS Management Console interface.  Continue to the Create Key Pair section.

You will need to create a Key pair to access your instance.  The key will be used to connect to your instance, if you lose it, you will need to create a new one and modify your instance to allow connection.  Don't lose it.  Give the key a reasonable name, and download the file and continue.

Now we must configure Security Levels.  These control the firewall that protects your EC2 instance.  Make a new Security Group (I called mine "basic"), and add an HTTP rule to the default SSH rule.  This allows access to your instance over ports 22 and 80\.  Continue to the review page, and after confirming that you are only launching 1 instance, and that it is Intance Type Micro, select the Launch button.  Close the pop-up message, and proceed to your "Instances" page (available from the left menu area within the EC2 tab of your AWS Management Console.  Micro instances take very little time to start, but if you happen to get to the Instances screen before it started, you will see a status "pending".  After a minute or two, you can refresh and your status will likely update to "running".

OK, the server is started, now we must connect to it.  Select your instance in the AWS Management Console and review the description details.  Near the bottom of the page is a Public DNS value, note this value.  If you are on Linux (non-Windows, really), launch your terminal application and connect via ssh using the key that you downloaded.  The quickest way to accomplish this is something like (the server is the value of your instance's Public DNS):
<pre>
ssh -i /path/to/my/keyfile.pem ec2-user@ec2-##-##-##-###.compute-#.amazonaws.com
</pre>
If you are on Windows, the easiest way (I think...) is to use [PuTTY and PuTTYGen](http://www.chiark.greenend.org.uk/~sgtatham/putty/).  You need to import your key file (*.pem) to PuTTYGen to make a *.ppk version that PuTTY can use.  Launch PuTTY and configure a new connection using the ec2-user@ec2-##-##-##-###.compute-#.amazonaws.com value as your host.  Then go to Connection - SSH - Auth, and use the Browse button to select a private key for authentication, choose the *.ppk file you generated using PuTTYGen.  Make sure to save the configuration, and open the connection.  

You should see the EC2 ascii art, with the Amazon Linux AMI Beta text.  At this point, the LAMP set up should be reasonably familiar, but remember that the Amazon Linux AMI is bare-bones.  We need to install Apache, MySQL, and PHP.  So, perform the following:
<pre>
sudo su
yum install httpd
yum install mysql-server mysql 
yum install php php-mysql
service httpd start
service mysqld start
/usr/bin/mysqladmin -u root password 'password'
</pre>

[UPDATE]
As Edward pointed out in the comments, this is a pretty bare-bones php installation. If you are installing a CMS or using a php framework, chances are high that you will require some or all of the following packages:

<pre>
sudo yum install php-xml php-pdo php-odbc php-soap php-common \ 
php-cli php-mbstring php-bcmath php-ldap php-imap php-gd
</pre>

After installing any PHP packages, or making config changes to PHP/Apache, you need to restart Apache before they take effect:

<pre>
service httpd restart
</pre>

Create a text file /var/www/html/index.php with the contents of your choice ("< ?php phpinfo(); ?>" is usually a good start - just remove the space from the opening php tag), and navigate to ec2-##-##-##-###.compute-#.amazonaws.com in a browser.  

That's it.  A LAMP stack in the AWS Free Usage Tier.  It won't be a speed demon (613 MB of memory...), but it's more than enough to test and practice.  I'll post basics for Elastic IPs, generating AMIs and failing over soon.
