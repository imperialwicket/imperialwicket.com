{
  "title": "AWS: Default root password",
  "description": "AWS: Default root password",
  "date": "2011-01-11",
  "url": "aws-default-root-password/",
  "type": "post",
  "tags": [
    "aws"
  ]
}
This article is more about general Linux than it is about Amazon Web Services or EC2\.  Nonetheless, quite a few people seem to be getting their fingers dirty with Linux servers as a result of the AWS free usage tier, and this question pops up regularly in the context of AWS.  I think more people using Linux and open source is awesome, so I want to cater to this crowd a bit.  

So, I get a lot of requests for information about the default root password in Amazon Linux.  The quick answer is:

**The default Amazon Linux EC2 instance does not allow root user login.**

If you need to do something as root (for example, add a new user), try the following:

<pre>
/usr/sbin/useradd myNewUser
</pre>
This returns a "Permission denied" error. Try the "sudo" command ("substitute user do" - most commonly the substitue user is root. The "!!" indicates that you want to repeat the last command issued.
<pre>
sudo !!
</pre>
You should see a "sudo /usr/sbin/useradd myNewUser" and the command will execute

Those of you who like to live more dangerously probably noticed that the following command does not work.
<pre>
su root
</pre>
The "substitute user [name]" command simply tells the shell you want to login as someone else.  By default the shell confirms the password.  A different option is to use "substitute user do" to switch to a superuser.
<pre>
sudo -s
exit
</pre>
Notice I immediately exit the superuser's shell.  This is because you should not be wandering around your server as root.

If you just needed to get to the root user's shell, there you have it.  For those interested, here is a little bit more information for your consumption.  The next question that pops up for me is, why don't I need to enter a password?  In Linux there is generally a sudoers file (/etc/sudoers for Redhat-like distributions - including Amazon Linux).  Check that file out here:

<pre>
sudo cat /etc/sudoers
</pre> 

The file is filled with comments and examples, but you'll notice the following two lines of content:

<pre>
root    ALL=(ALL)    ALL
ec2-user    ALL = NOPASSWD: ALL
</pre>

Without going into much detail, I'll highlight the obvious here:  Notice the "ec2-user", "ALL", and the "NOPASSWD:ALL"?  That's right, the ec2-user is allowed use the "substitute user do" command, and (without a password) perform all commands from anywhere.  Luckily, one of those commands is "sudo -s".  For the curious, this means that the following also work (make sure you are logged in as ec2-user first):

<pre>
sudo su root
exit
sudo su
exit
</pre>

For anyone coming from Windows, putting "sudo" in front of a command is like participating in the operating system as an administrator - so long as your user is in the sudoers list ([be careful](http://xkcd.com/838/)). 

Now for the password, if you have not already, let's make a user:

<pre>
sudo /sbin/useradd myTestUser
</pre>

Now let's take a look at the user list, where you should see "myTestUser" at the bottom.

<pre>
sudo cat /etc/passwd
</pre>

And investigate the real user list:

<pre>
sudo cat /etc/shadow
</pre>

Notice the second value for many of those entries consists of "!!".  Let's go ahead and explicitly set the password for "myTestUser", and then review the shadow file.

<pre>
sudo passwd myTestUser
[Enter password twice]
sudo cat /etc/shadow
</pre>

At this point the shadow file is updated to include the encrypted password and salt for "myTestUser".  I think that's a good amount of information to get everyone started with user management in Amazon Linux.  There is a wealth of data out there on Linux users, and if anyone has questions, feel free to post in the comments.  This should be sufficient to acquire root level access to your instance (remember that using "sudo" is almost always a better idea than simply "sudo -s"-ing as soon as you login to your instance), and give you a little bit of knowledge about why/how the access is available to you.
