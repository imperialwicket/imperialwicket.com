{
  "title": "Goodbye shared hosting; Hello AWS Heavy Utilization Reserved Instance",
  "description": "Goodbye shared hosting; Hello AWS Heavy Utilization Reserved Instance",
  "date": "2011-12-02",
  "url": "goodbye-shared-hosting-hello-aws-heavy-utilization-reserved-instance",
  "type": "post",
  "tags": [
    "aws",
    "linux",
    "dev"
  ]
}
I just trashed a drafted post mortem discussing a year of experience with Amazon Web Services under the free usage tier. I trashed it because Amazon just launched updates to their Reserved Instance options.

This is a  brilliant move. I know this offering will allow a lot of higher usage organizations to save substantial amounts of money. This means that AWS's pricing edge on other cloud providers just became a lot more attractive to anyone interested in moving to the cloud, or selecting a cloud service provider. But accompanying some of the lost revenues from savings are all of the low usage individuals and small organizations who have been muddling around in micro instances under the free usage tier for its first year.

### Moochers, Testers, and Cautious Early Adopters

I thoroughly enjoyed AWS during the past year. It made it extremely easy to fire up a server and run tests or try new technologies, and without the burden of DNS/domain registry, account generation, or simply working on local servers ([I documented some of the things I tried over the year.](http://imperialwicket.com/tag/aws)). I did not sign up for AWS to take advantage of the Free Usage Tier upon its immediate availability, but I did not wait long. So you know, those of us who signed up for AWS during that first month after Free Usage Tier was announced are losing those benefits about now. My perks expired Dec 1\. Hence, yesterday I shut down the one micro instance I had running, shed a micro tear, and wondered how much I would really continue to use AWS now that it's impending charges were more apparent.

I knew that I would keep the account alive, and I would certainly use AWS for the occasional need. But over the last year, I did not think twice about spinning up an EC2 instance and trying out some new technology stack. And I liked it. I felt a little bit of that excitement die yesterday. Don't take me the wrong way, I don't feel cheated by AWS, in fact I more feel cheated by my own frugality, but that was the scenario on Dec 1.

### Lo! Wait, Free Usage Tier-ers!

And then at 5AM on Dec 2, as if talking directly to me, AWS notifies me of the great savings I can experience using [new Light/Heavy Utilization Reserved Instances](http://aws.amazon.com/ec2/reserved-instances/). What to do...

My original post about the end of my Free Usage Tier went like this: sadness, too bad, thanks amazon, and some decision process - by the way. The decision process was basically this: I have virtually no administrative overhead for my cheap shared hosting, and my cheap shared hosting is cheap. Shared hosting performs well enough for my needs, and if I really need root SSH access or a clean server for something, I can always fire up an EC2 instance for a few days, and then just shut it down. Cost comparison is something like $15/mn for an on demand Micro instance. Using one of the old reserved instances brought you down to under $10.50/mn. I admit, that number was already very close to the tipping point for me. $10.50 is not much more than I pay per month for hosting on a shared Linux machine. But again, I have NO administrative overhead. Do I really want to pay a few more dollars a month to have more work to do (yes, I sort of do, but what about my $2.00 per month...)?

### Micro site hosting estimates

[Updated with additional explanation and fixes for some fuzzy math]

Some numbers, so you don't have to bother yourself with the maths. I'm only calculating EC2 instance cost here, if you run high bandwidth (lots of uploads or site hits), or use massive amounts of storage, those will obviously add to your total cost.  I'm working with an 8,760 hour year and assuming 100% instance utilization.

##### On demand Micro instance

$175.20 annually, $14.60 monthly

##### Micro instance with former (now Medium) Reserved Instance discount

1 year: $115.32 annually, $9.61 monthly
3 year: $265.96 total, $88.65 annually, $7.39 monthly

##### Micro instance with new Light Utilization Reserved Instance

1 year: $128.12 annually, $10.68 monthly
3 year: $350.36 total, $116.79 annually, $9.73 monthly

##### Micro instance with new Heavy Utilization Reserved Instance

1 year: $105.80 annually, $8.82 monthly
3 year: $231.40 total, $77.13 annually, $6.43 monthly

Summary: under $7.00 monthly.  Did you catch that?  Full root access to a cloud instance with 613MB RAM for less than $7.00 monthly.  Note that you do have the up front cost of $100 over a three year term.  Not a huge cost, but that does include a non-usage based fees.  It's $62 annually if you want to lose some savings, but make it a little safer to leave early.

### My Plans

$6.42 is less than I pay for shared hosting now (although affiliate and new account kick-backs offset most of the cost). That in mind, I think I will plan to ditch my shared Linux hosting, and I will likely set up 1 HURI (3yr) and 1 LURI/MURI (probably for 1yr), knowing that my primary sites will run off the HURI and I'll likey use an instance for testing/playing between 30% and 50% of the time.

There's definitely the administrative overhead to address, and some backup configurations that I didn't have to do manually before, but who am I kidding?  I like doing those things - particularly on my personal projects.

Thanks, AWS. I'm glad we can continue our relationship in a more meaningful way.
