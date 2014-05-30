{
  "title": "AWS: Password-less SSH and SCP on Amazon Linux",
  "description": "AWS: Password-less SSH and SCP on Amazon Linux",
  "date": "2011-07-02",
  "url": "aws-password-less-ssh-and-scp-on-amazon-linux",
  "type": "post",
  "tags": [
    "aws"
  ]
}
First of all, you don't need to enable or install FTP to copy files from one place to another.  SSH (and SCP) can accomplish this task (better than FTP can...).  Second, you don't always need to connect to Amazon Linux as ec2-user.  This is really only necessary the first time.  

#### To FTP, or not to FTP

If you absolutely must have FTP, configuring FTP for Amazon Linux is as easy as installing 'vsftpd' package, making some subtle configuration file updates, and opening ports to your EC2 instance.  There is a great tutorial on [vsftpd installation on Amazon Linux at That's Geeky](http://www.thatsgeeky.com/2010/11/installing-vsftpd-on-amazons-linux-ami/).  FTP is fine under some circumstances, but from a security standpoint, it comes with its share of flaws.  FTP requires opening an absurd number of ports (or switching to active mode - which inevitably confuses your users), it sends data in the clear, and I just don't like it.  Not to mention the fact that SCP is already available, since it uses SSH protocol and port 22 (which you have already in the Amazon Linux AMI).

#### Configuring SCP connections

Configuration SCP connections is really nothing more than creating users.  Since I harped on security a bit, I think it is prudent to configure connections via rsa keys.  If you are using Amazon Linux, your ec2-user is already configured to connect via rsa key, and all we need to do is provide additional keys to the server so that other machines can connect.

There are lots of tutorials out there for configuring password-less linux connection over SSH.  The issue I encountered is that most of these tutorials (at least the recent ones) use ssh-keygen followed by ssh-copy-id, which assumes that you can already connect with a password.  Password connectivity is not enabled by default in Amazon Linux, and why should we bother enabling it?

I am going to use the following conventions in the shell:

*   111.111.111.111 - the elastic ip for my EC2 instance
*   ec2-user.pem - the private key that Amazon generated for me, which I use to connect as ec2-user
*   myFedora - the private key that I generated on a laptop that needs to connect to my EC2 instance
*   myFedora.pub - the public key from the myFedora key pair
*   myMac - the private key generated from another another laptop that needs to connect to my EC2 instance
*   myMac.pub - the public key from the myMac key pair

The basic steps are:

1.  Create the users on the EC2 instance
2.  Generate and/or collect the public keys for those users
3.  Copy the public keys to the EC2 instance
4.  Update the authorized_keys files for the users
5.  Connect without passwords, and allow each user secure connectivity via SCP

#### Create users on the EC2 instance

I am going to create users myFedora and myMac (matching my key pairs).  In my case, these represent two machines that I use regularly.  I will login to an EC2 instance, then use adduser to create the users.

<pre>
ssh -i .ssh/ec2-user.pem ec2-user@111.111.111.111
sudo adduser myFedora
sudo adduser myMac
</pre>

#### Generate and/or collect the public keys

As many tutorials detail, generating a public key is as easy as:

<pre>
[imperialwicket@myFedora ~]$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/imperialwicket/.ssh/id_rsa): /home/imperialwicket/.ssh/myFedora
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/imperialwicket/.ssh/myFedora.
</pre>

Notice I elected not to use the default "id_rsa" key file name; if everyone uses the default, they all look the same.  If you do not plan on remembering your passphrase, then leave it blank.  If you lose it, you need a new key.

Collect all the public keys on a single computer, with access to the EC2 instance as ec2-user.

#### Copy the public keys to the EC2 instance

Now that we have the keys, we need to SCP them to your EC2 instance as the ec2-user.

<pre>
scp -i .ssh/ec2-user.pem myFedora.pub ec2-user@111.111.111.111:/home/ec2-user/
scp -i .ssh/ec2-user.pem myMac.pub ec2-user@111.111.111.111:/home/ec2-user/
</pre>

#### Connect as ec2-user and update the authorized_keys files

Connect as the ec2-user.  I am going to do some user dancing, because I am trying to reduce my overall time spent as root (and you should too).  As the new users, create a .ssh directory and chmod it to 700 (drwx------).  Then create an authorized_keys file in the new .ssh directory and chmod it to 600 (-rw-------).  If these permission are wrong the connection will not work; you will see unhelpful debug output with '-v' and the only error will be "Permission denied (publickey)."  You will also need to be sure that your local permission are correct, 644 for your *.pub, 600 for your private key, and 700 for your .ssh directory.  This enforcement ensures that no other users can edit your keys on either side.

This is not the quickest way to do this, and I am avoiding '~' (home dir) notation in the interest of making things as explicit as possible.  To be honest, this is definitely an instance when I would want to open my session with 'sudo su -'; but as I mentioned, I am trying to break myself of that habit.

<pre>
ssh -i .ssh/ec2-user.pem ec2-user@111.111.111.111

sudo su myFedora
cd
mkdir /home/myFedora/.ssh/
chmod 700 /home/myFedora/.ssh/
touch /home/myFedora/.ssh/authorized_keys
chmod 600 /home/myFedora/.ssh/authorized_keys
exit

sudo su myMac
cd
mkdir /home/myMac/.ssh/
chmod 700 /home/myMac/.ssh/
touch /home/myMac/.ssh/authorized_keys
chmod 600 /home/myMac/.ssh/authorized_keys
exit

sudo mv myFedora.pub /home/myFedora/.ssh/
sudo chown myFedora:myFedora /home/myFedora/.ssh/myFedora.pub
sudo mv myMac.pub /home/myMac/.ssh/
sudo chown myMac:myMac /home/myMac/.ssh/myMac.pub

sudo su myFedora
cat /home/myFedora/.ssh/myFedora.pub >> /home/myFedora/.ssh/authorized_keys
exit

sudo su myMac
cat /home/myMac/.ssh/myMac.pub >> /home/myMac/.ssh/authorized_keys
exit
</pre>

#### Allow other users to connect securely via SCP

Using Terminal (Mac), PuTTY (Windows), or Gnome Terminal (GNU/Linux with Gnome), connect to the server:

<pre>
ssh -i .ssh/myMac myMac@111.111.111.111
</pre>

Now you can configure SSH/SCP connection to the server in Fugu/Cyberduck (Mac), WinSCP (Windows), or Nautilus (GNU/Linux with Gnome).  Simply tell the program to make an SSH connection to the 111.111.111.111 server as user 'myMac' and it will use the existing SSH connection.  If you are using Windows, WinSCP will let you establish an SCP connection using a key, which bypasses the need for establishing the connection in PuTTY first.  I have not figured out a way to pass the rsa key to Fugu or Nautilus, which is the reason for opening the Terminal connection.  If anyone knows how to give Nautilus/Fugu (or if Cyberduck handles this better) the location of the private key, I'm all ears.

#### Stop using FTP

FTP sends everything in the clear.  Start asking for SCP/SSH/SFTP access to servers, it's better for everyone.  I hope this helps a few people make the switch from FTP to SCP.  
