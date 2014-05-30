{
  "title": "Chicago Boss: My CB deployment strategy",
  "description": "Chicago Boss: My CB deployment strategy",
  "date": "2012-07-12",
  "url": "chicago-boss-my-cb-deployment-strategy",
  "type": "post",
  "tags": [
    "aws",
    "erlang",
    "chicago boss",
    "git"
  ]
}
### Summary

This is the solution I am using for smaller services and sites where automatic scaling is likely overkill. You can easily enhance this to support a handful of servers, beyond which something like puppet/chef is probably in order.

At a glance:

*   EBS backed EC2 instance with key access for my user
*   Nginx - configured to serve static content, and reverse proxy to mochiweb for CB content
*   Separate user for Chicago Boss and the apps
*   Chicago Boss - cloned from Github for easy updates
*   CB Admin - cloned from Github for easy updates
*   My app - cloned from the server git repository, and updated with post-receive hook

### AWS

Generally, I use a vanilla Amazon Linux server, and configure it with my own user for SSH connection via keys only. I update my user to have passwordless sudo permissions, and usually I disable the ec2-user entirely. You will need to disable 'Defaults requiretty' in /etc/sudoers for the git hook to work properly, you can just comment the line.

I explained basic SSH key authentication when describing [more secure lamp on AWS](http://imperialwicket.com/aws-quick-and-secure-lamp-on-amazon-linux). You can ignore pretty much everything but the terminal code for ssh keys and disabling password based authentication. There is no need to install MySQL, Apache, etc.; unless you plan to use MySQL as a database for Chicago Boss, but that's beyond the scope of this post.

I detailed a [Chicago Boss installation on AWS EC2](http://imperialwicket.com/aws-install-chicago-boss-on-amazon-linux) and the only particular skipped in that article is that I installed all of these under a particular user, which I will call 'app' in this post. Nginx is detailed briefly in that post, and redirects to the helpful page from the CB wiki.

After spinning up your EC2 instance and creating your personal user, whom I will call 'me', make sure that you can connect via 'ssh 11.22.33.44' - where 11.22.33.44 is your Elastic IP (or public dns value, but you should always consider using an Elastic IP - it makes a lot of things easier). 

Update the server, install git, and create a bare repo under your user's home directory.

<pre>
sudo yum update
sudo yum install git
cd 
mkdir app.git
cd app.git
git init --bare
</pre>

### Users/Permissions

Chicago Boss does not require root permissions and we will install our application(s) under an app-specific user. You should have a 'me' user present, and that's who we'll always connect as.

Now we need to create an 'app' user, and add 'me' to the 'app' group. We'll also give the 'app' group write access to the 'app' user's home directory. Add the 'app' user to the 'me' group so that 'app' can clone from the git repo in /home/me/.

<pre>
sudo su -
useradd app
usermod -Ag app me
usermod -Ag me app
chmod 750 /home/me
chmod 770 /home/app
exit
</pre>

After pushing your CB project from a local machine to the bare repo we created on the server (this likely involves something like 'git remote add origin 11.22.33.44:/home/me/app.git' in your local repo, followed by 'git push'), you can clone your application, Chicago Boss, and cb_admin from Github and the repo on your server.

<pre>
sudo su - app
cd /home/app/
git clone https://github.com/evanmiller/ChicagoBoss.git
git clone https://github.com/evanmiller/cb_admin.git
git clone ../me/app.git
exit
</pre>

You'll need to execute 'make' in the ChicagoBoss directory, clean up configs, etc. All of these actions are detailed in the linked articles, and if you encounter any issues, add a comment and I'll do my best to update things or point you in the right direction. 

### Git post-recieve hook for Chicago Boss

This was a little tricky, primarily due to permissions/users/ownership that I wanted to enforce. Also, there is the fact that rebar really likes to run with the user in its directory. For example, as user 'app':

This doesn't quite work:
<pre>
cd /home/app/
./app/rebar compile
./app/init.sh reload
</pre>

But this does:
<pre>
cd /home/app/app/
./rebar compile
./init.sh reload
</pre>

I didn't spend a lot of time with this, and it may just be an update that's needed to rebar and init.sh, but for now, I just dealt with it in the post-receive script, which looks like this:

##### /home/me/app.git/hooks/post-receive:

<pre>
#!/bin/sh
#
# Checkout updates in the CB app directory, compile, and  hot deploy.

# Set GIT_WORK_TREE and checkout
sudo -u app GIT_WORK_TREE=/home/app/app/ git checkout -f

# CB specific compile and reload
sudo su - app -c 'cd /home/app/app/; ./rebar compile'
sudo su - app -c 'cd /home/app/app/; ./init.sh reload'
</pre>

This is overly complex, and it is most due to my desire to disallow direct login as my app user. A lot of the more intricate sudo behavior is solely to allow the command to be executed remotely as the 'me' user, and would be unnecessary if you simply connected to the server as the same user who will host your app. There are lots of other ways to handle it (using /var/www/html, or /opt, or whatever and handling permissions there, for example), but I think it's easy to understand what's happening at a glance with this config.

With that post-receive hook in place, you can push to your remote repository at 11.22.33.44:/home/me/app.git, and after each push, the checkout occurs in your /home/app/app directory, and git automatically issues './rebar compile' and './init.sh reload' as the appropriate user from the appropriate directory.

It's not the easiest setup to get running, but it is simple once you complete the config. A bonus is that if anything goes awry in a push, compile will likely fail, and the server will simply maintain the old compiled files, so end users see the same behavior that was live before your update - and you can take your time and address issues.

Happy deploying!
