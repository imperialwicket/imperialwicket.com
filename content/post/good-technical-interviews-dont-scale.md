{
  "title": "Good Technical Interviews Don't Scale",
  "description": "Why your best technical interview will be the first that can't keep up with growth.",
  "date": "2024-12-03",
  "url": "good-technical-interviews-dont-scale/",
  "type": "post",
  "tags": ["mgmt"],
  "draft": false
}
We all know that technical interviews are broken. Or so say the majority of the folks on the internet who have an opinion to share. I want to join the side of the argument who admits that something isn't working for technical interview models, but I think "broken" is the wrong word. I want to argue that technical interviews tend to optimize for the wrong things, and the fundamental problem is optimizing for scale. How repeatable is the interview? How many interviewers can effectively run this interview loop? What candidate through-put can we reach? What's our effective time-to-hire with this interview? How consistent are our output metrics for candidates experiencing this interview? Do you see how none of these questions are anything like: how well will this candidate perform in this position?

# An ~Excellent~ Unscalable Interview

One of my earliest endeavors in "management" was figuring out how to hire senior engineers in the DevOps/SRE space. While the particular interview only survived the organization's scaling for a handful of years, it remains a source of pride for me and I still think we should have kept using longer than we did. I have talked with hundreds of candidates for various roles/levels, and I've given this particular interview almost fifty times. I have also run dozens of interview moderation/calibration discussions for mostly senior SWE/SRE type roles. With the right interviewer, it is my **very strong** opinion that this interview provides better signal on nearly everything that matters for technical roles with nebulous day-to-day requirements.

# The Technical Challenge

At its core, this interview is short and straight-forward. It's a small list of tasks that you relay to the candidate at the beginning of the exercise. Then you wait, watch, discuss, guide. The whole process is as follows.

## Pre-game

Tell the candidate that interview will be a one hour long technical exercise, with 15 mins devoted to introduction and review discussion (first 5m and last 10m). The exercise is to be completed remotely. We will require connection(s) to a server in AWS cloud running Ubuntu, and they will need to be able to share their screen as they make changes and improvements to the server.

## The Technical Exercise

You are on an Infrastructure Team, and someone from Marketing reaches out to ask for help with a new project. They need you to install a Ghost blog on one of their existing servers so that they can use it for external documentation. During the initial review the blog needs to be password protected, Security said something about "basic auth should do." We'll assume the request is valid, and we do need to prioritize this work as requested. 

You must:
 - Share your IP so that I can expose a server to you
 - Install a ghost blog 
 - Ensure the ghost blog isn't publicly accessible
 - Disable password based ssh authentication on the server

Note for Interviewer: read this to the candidate. If they ask for the text, pass it along, but do not provide it without prompting. The ordering is intentional.

## Background And Instructions For Interviewer

The server in question is a fairly vanilla Ubuntu Server. It is intentionally an old (EOL'd is even better...) LTS release. The candidate user has sudo permissions via their password. The server notably has some stubbed html files with readme details that effectively say, "I must stay up, don't delete me - talk to $someTeam." These files are served from Apache's default www directory and Apache is running on the server. If you attempt to access the server via web browser, it shows a page that is clearly a marketing demo of some sort. 

Information to share with the candidate:

1. The server is a small-ish EC2 instance protected strictly with a security group that explicitly allows requests from particular IPs (interviewer and interviewee) over ports 22, 80, and 443.
2. You should use any and all resources, and you should absolutely ask any quesitons you have. The worst thing that will happen as a result of a question is that you will be directed to find that information on the internet.
3. Inform the candidate that you do not actively use ghost within your organization. However, it is often necessary to figure out how to get some solution up and running as efficiently as possible, and then to come back for tuning/maintenance as necessary. This technical exercise is meant to show us how a candidate deals with completing new tasks.
4. Please share any/all windows or screens such that we can watch SSH/Terminal activity as well as browser searching together and we can collaborate on completing these tasks. 
5. Please provide your IP and we can adjust the security group while you address any screen sharing configuration updates.
6. Once IP access is granted, provide the candidate with the user and password for ssh access. They should look something like `candidate` and `CHANGE_ME!`.
7. Let the candidate know that you will inform them when there are 15 minutes remaining in the session (meaning they have 5 more minutes to work and then 10 minutes for review and discussion).

During the exercise you must use your judgment so as not to hand-hold a candidate through the exercise. Nonetheless, if they ask a direct question, you should almost certainly provide them a direct answer. For example, "Wait: does apache need to be running?" warrants something like, "if it's already running, I assume it should keep running." Similarly, if you see something that feels like a dead end or you have a genuine question about where things are going, just ask. Example: "I'm not sure we need to install and enable SELinux in order to complete the task, do you feel like it's necessary?" or "I think Nginx won't start because Apache is probably running."

## Closing discussion and questions

In running this technical exercise nearly fifty times, I never encountered two that felt the same. However most of these questions hold up for anyone who manages to get very far into the challenge. In the 45 minutes of work, I never had a single person complete all the tasks. About a quarter of the candidates managed to get a ghost blog functioning, and half of those individuals ended up receiving offers. It's a small pool, but no one disappointed. 

1. In hindsight, would you have organized your efforts any differently?
2. Assume we completed our tasks, what would you want to do before enabling this for "production" use?
3. (If not addressed in 2) Related to production readiness - do you have any scaling/reliability concerns?
4. Would you use ghost? If so, in what application? If not, why not?
5. Do you have any questions for me?

# Why This Exercise Provides Great Signal

When I have led this exercise, I found it gave great insights around existing knowledge, ability to transfer knowledge across domains, and how interested in learning new things a candidate is. Here's a walkthrough on some of the _gotchas_ that are sprinkled throughout to inform the interviewer.

## Intro
**Did the candidate show up in a ready state to participate?** Pre-game information was shared. If the candidate is using an ISP that blocks port 22 it's a red flag. If they ask what OS the server is running, they weren't paying attention. If they ask specifically, "Which Ubuntu release is this?" you get to say, "I'm not sure, do you know where we could look to determine that?" Do they know their IP or how to get it? I've had multiple people first tell me that their IP is 127.0.0.1, or similar local-network values. Do they stumble in getting camera or screen sharing to function? These are all signals around comfort or preparedness.

**Did the candidate understand the assignment?** You should have read the assignment to the candidate. Did they write it down? Did they ask for a copy of it? Do they remember it without prompting you to repeat? Did they ask questions about the tasks, like "Do I need to complete this in order?"

## Some Exercise considerations
**Does the candidate stick to the tasks, or do they branch to other work?** When you login to an old Ubuntu server, it often tells you that updates are needed. Do they ask about performing updates? Do they just run the updates? Do they assume that they can escalate to root-level permissions? How do they use privilege escalation? Their password literally says, `CHANGE_ME!` do they change the password? 

**SSH config:** There are a couple pitfalls in SSH config files and service restarting. Does the candidate configure a personal key? Do they configure the service to disable password authentication successfully? Do they actually test it? Do they lock themselves out of the server? Do they do any of these things knowingly? Did they do SSH updates last (as given in the task), or prioritize that as an easier/quicker effort?

**Ghost:** Ghost is more well known now than it was when this exercise was originally created, but it tends to be less familiar to folks I've interviewed than something like Wordpress or a static site generator. Can the candidate find the ghost blog docs? Do they follow the Ghost docs exactly, or use them as a guide? On the old Ubuntu server, you must install nodejs manually if you want a version that's compatible with the latest ghost release. How do they install nodejs? PPA? Local build? Install an incompatible version? Install some container solution and find a container image? For data storage ghost will pretty happily use SQLite, MySQL, or Postgresql - what do they use? How do they install and configure MySQL or Postgresql if they use one of those solutions? Ghost defaults to serving on a non-standard port via nodejs, does the candidate try to get nodejs to serve ports 80 and 443? Do they reverse proxy? Do they attempt to install Nginx (as the Ghost docs suggest)? How do they troubleshoot Nginx service starting when Apache is already using ports 80 and 443? Do they just kill Apache? What do they do with the existing web pages? Does the candidate make any decisions based on their own experience? Comments like "I'm going to use Nginx instead of Apache, because I'm more familiar with it." make for interesting follow up discussion.

**Basic Auth:** Does the candidate know what basic authentication is? Do they leverage Apache/Nginx/similar to get password protection in front of a web service/directory? Do they attempt to password protect in a different way?

## Closing Discussion

**Time management:** If someone waited to address SSH config updates and didn't even get to them, do they recognize that they could have completed that first? Some candidates will get lost in some rabbithole like building Docker on the server instead of trying a non-container solution. Some people will find instructions to install ghost from a random website and the instructions are for a newer OS release, should they have spent more time researching? Was the candidate surprised when you announced the 15 minute warning?  

**Production readiness/Scalability/Reliability:** Did the candidate use SQLite for data? Did they install MySQL or Postgresql locally? Do they talk about configuration management tools or infrastructure as code? Do they consider SSH user management or security groups? Do they have questions or insight regarding resources necessary to support this site? Do they ask about traffic expectations (if they haven't already)? How are site admins managed? How is the site admin section protected? How are ghost users managed? Do they talk about CDNs or caching? SSL Certificates?

**Ghost Impressions:** How do they frame technology questions and impressions? Do they suggest something like a static site, although the userbase for this solution is likely less technical? Do they gravitate toward some other technology they know? Do they suggest using it but paying for someone else to host it? Was this a waste of time? Do they value the experience of learning about a potentially new software solution? 

**Questions:** In my experience running this interview compared to other interview questions/sessions, the final question for this tend to skew toward the technical implementation and other ways that it could be completed. 

# Why This Can't Scale

Just like any good challenge, once people know and can prepare, it's not a fresh new investigation anymore. If your organization is big enough that folks are sharing interview questions, then this technical challenge loses most of its value. 

Leading this category of technical exercise also requires a lot of random experience and expectation for the interviewer. I think it's much harder to find someone who can successfully lead an interview like this (and effectively assess the result) than it is to find someone who can watch candidates write fizzbuzz loops. 

The assessment matrix for this type of interview is more hand-wavy and subjective. There are some built-in check boxes (Changed SSH config, installed x/y/z, etc.), but the pass/fail model isn't really oriented around how many boxes someone checks. I've definitely proceeded with a job offer to folks who did very poorly at checking boxes, but we're able to reason through the effort well and displayed that they could effectively learn on their own. 

In a small-ish organization where you want to hire and retain a world-class team of scrappy technologists who will solve any problem you throw at them, I think this type of challenge can be a means to success. As your organization grows and your hiring needs become more of a supply chain and less of search for the perfect fit, this simply stops working. It's a bit sad, but that also shows you the organization size I prefer, doesn't it?
