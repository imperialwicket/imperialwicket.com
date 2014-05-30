{
  "title": "Amazon Web Services (AWS) Free Usage Tier Analysis",
  "description": "Amazon Web Services (AWS) Free Usage Tier Analysis",
  "date": "2010-10-31",
  "url": "amazon-web-services-aws-free-usage-tier-analysis",
  "type": "post",
  "tags": [
    "aws"
  ]
}
I am excited for the AWS Free Usage Tier to officially become available.  To their credit, Amazon already offers much of the Free Usage Tier to its existing customers (SimpleDB, Simple Queue Service, and Simple Notification Service are all free to a certain limit for all customers).  Also to their credit, Amazon's marketing team did well pushing the Free Usage Tier, because in a lot of the write-ups I read, there was no mention of the fact that it is ONLY available to new customers, and after one year, the Free Usage Tier evaporates, and you pay the normal, pay-as-you-go amounts.  

Using the AWS-provided [Simple Monthly Calculator](http://calculator.s3.amazonaws.com/calc5.html), the approximate cost equivalent for the AWS Free Usage Tier is just over $38 per month.  At a glance, you are getting a mid-level VPS with a number of useful bells and whistles, for the price of a basic VPS with high up-time (plus a free introductory year).  Seems like a good deal to me, even after you conclude the year of Free Usage Tier use.

One item worth noting is that to date, there is no way to cap your spending in AWS (at least not that I have foiund...), so if you fire-up extra EC2 instances, and forget to turn them off, your AWS bill jumps from $0 to however much you used.  I need to remember to be careful with this.

Interestingly, AWS Free Usage Tier gives you access to an Elastic Load Balancer instance.  This is meaningless without multiple EC2 instances, so if you are planning to run a site 24/7 that is using ELB, be prepared to pay for that second EC2 instance.  That said, if you just want to test AWS itself, you can fire up the Elastic Load Balancer, start 30 EC2 instances, run them all for a day, shut everything down, and pay nothing.  Again, just pay attention to what you are using, and familiarize yourself with the limitations of the Free Usage Tier.

As Amazon states, you could run multiple static web pages from S3 alone, and this might be an interesting option for the few dozen completely static sites that are still out there.  However, during your Free Usage Tier period, you can easily run several low-bandwidth dynamic sites with a single EC2 instance and the free SimpleDB offering.  I doubt that many people will use this option, as the numbers are barely enough to support it, and if this is all they need, the < $10/mth options from the popular shared hosting sites make a lot more sense over the long term.  I see the Free Usage Tier as a call to higher-end users who may be interested in experimenting with AWS as a replacement for their VPS farms, but couldn't justify doubling their monthly service costs for a trial period and a cut-over period.  AWS Free Usage Tier is perfect for this testing and implementation transition.  For these users, the Free Usage Tier just allows an inexpensive testing and cut-over, and based on my calculations, they will also end up saving money through AWS usage after the first year is over.

I plan to sign up for an account tomorrow and spin up as many EC2 instances as I like for testing and working with AWS during moonlight hours.  I also plan to take them down each night.  Using the 5GB of available S3 storage, I can store images of the EC2 instances, and get multiple instances back up and running quickly and easily each night I want to 'play' with AWS.  Testing ELB services and increasing my familiarity with the AWS Management Console are on my to-do list, as well as trying some of the common browser plugins and smartphone interfaces.  At the very least, I hope to continue to enhance my Linux knowledge, and learn some of the ins and outs of the AWS.  I'll post initial reactions next week.

<span class="signature">
// TODO habari theme(s), GWT/GAE
// --imperialWicket
</span>
