{
  "title": "AWS: Install Chicago Boss on Amazon Linux",
  "description": "AWS: Install Chicago Boss on Amazon Linux",
  "date": "2012-03-01",
  "url": "aws-install-chicago-boss-on-amazon-linux/",
  "type": "post",
  "tags": [
    "aws",
    "linux",
    "erlang",
    "chicago boss"
  ]
}
_Something of an aside: I am finally making some time for Erlang, and have a couple neat projects in the pipeline for which I think it will prove appropriate. I want to focus these projects around server APIs with thick client-side code. I am leaning toward [Chicago Boss](http://chicagoboss.org/) with [Spine](http://spinejs.com/). Before I commit to that, I want to spend a little more time with [Zotonic](http://zotonic.com/) and I would like to get to know [Nitrogen](http://nitrogenproject.com/). On the front-end, [Backbone](http://backbonejs.org/) is a possible alternative, and I may avoid the client-side focus altogether and use the templating available in the Erlang framework._

I am going to skip the instance launching steps; you can find those spattered about the web. I put a [LAMP targeting AWS EC2 instance article](http://imperialwicket.com/aws-building-a-lamp-instance) together that would do nicely, and you don't need to run any of the installations, just launch the instance and make an ssh connection. You might want to run a yum update.

The Chicago Boss installation turned out to be relatively straight-forward, but like [installing Erlang](http://imperialwicket.com/aws-install-erlang-otp-on-amazon-linux), there were a few time-consuming parts that I wanted to document. The basic steps are:

1.  Install a reverse proxy server (Nginx)
2.  Install Erlang
3.  Install Chicago Boss
4.  Create a test project in Chicago Boss

### Install Nginx

If you are only testing Chicago Boss, and do not plan on immediate use in a production environment, there is no necessity for a proxy server. The proxy server just gives you easy access to Chicago Boss over port 80\. If you skip  that step, just be sure that you add port 8001 to your security group.

As long as you are ok with an older Nginx release (in this case, I am), you can install Nginx from the Amazon repositories.

<pre>
sudo yum install nginx
</pre>

The download, configure, make, make install procedure for a more recent Nginx version does not seem difficult, but I did not run through it and it is outside the scope of this effort. I will note that if you are using setting the IP explicitly in your Nginx configuration files, be sure to use the Private IP and NOT an Elastic IP. You will encounter bind errors like the following if you use an Elastic IP:

<pre>
[emerg]: bind() to 11.22.33.44:80 failed (99: Cannot assign requested address)
</pre>

As I said, just replace the Elastic IP with the Private IP, and Nginx will start properly.

You will want to configure Nginx to relay requests to port 8001, there are [instructions available from the Chicago Boss wiki](https://github.com/evanmiller/ChicagoBoss/wiki/Deploy) for this redirection. That's not a great production config, but it is more than adequate for getting started.

### Install Erlang

Get the latest download path [here](http://www.erlang.org/download.html), and [these Erlang installation instructions still work](http://imperialwicket.com/aws-install-erlang-otp-on-amazon-linux). Blogs are handy sometimes.

### Install Chicago Boss

Download the [latest release](https://github.com/evanmiller/ChicagoBoss/downloads), and make it.

<pre>
wget https://github.com/downloads/evanmiller/ChicagoBoss/ChicagoBoss-0.7.2.tar.gz
tar xzf ChicagoBoss-0.7.2.tar.gz
cd ChicagoBoss-0.7.2
make
</pre>

I wanted to install the admin interface, for which there are instructions in the [cb_admin repo](https://github.com/evanmiller/cb_admin) and also in the [Chicago Boss GH wiki](https://github.com/evanmiller/ChicagoBoss/wiki/Admin-interface). However, I had issues getting either to work, so I will follow up with a cb_admin post.

### Create a project with Chicago Boss

We are up and running now, and the next step is definitely to create a project and start pushing Chicago Boss to meet your needs. Rather than copying the existing docs or paraphrasing, I will redirect everyone to the  [Quickstart wiki page](https://github.com/evanmiller/ChicagoBoss/wiki/Quickstart) and the more verbose [Tutorial pdf](http://chicagoboss.org/tutorial.pdf). I recommend both.

If you encounter issues or have questions, the [mailing list](http://groups.google.com/group/chicagoboss) seems to be the way to go.
