{
  "title": "Getting Started In Open Source",
  "description": "Getting Started In Open Source",
  "date": "2011-01-01",
  "url": "getting-started-in-open-source",
  "type": "post",
  "tags": [
    "teachingopensource"
  ]
}
A few days ago, I came to the decision that [I need to start doing more to support open source](http://imperialwicket.com/new-years-resolution-more-open-source-participation).  My goal is to participate in a couple of open source projects that are important to me ([GeoServer](http://geoserver.org) and [Habari](http://habariproject.org)).  More specifically, I plan to submit code, bugs, and/or documentation during 2011\.  Another goal of the effort is to document much of the endeavor for each project.  To that end, here are my thoughts for selecting projects and getting started.

#### Choosing an open source project to support

I have multiple goals in choosing open source projects to support.  My primary goal is reciprocal in nature.  As mentioned in my resolution post, I want to ship more.  Assuming that I can provide clean, functional, and consistently formatted code, this works out well for projects that are willing to accept my support.  The projects get code updates (bug fixes, feature additions, etc.), and I get to ship code (do something tangible).  

First runner-up is my goal to build on my skill set.  In the ever-changing world of IT, programming, sysadmin, etc., it is of the utmost importance to stay current.  I don't think it is necessary to jump ship every time something new and interesting comes along, but with good measure, I think I need to expand my skills.  My skill enhancement goals are not limited to languages or packages, but also include version control systems, documentation, project tools, processes, and more.  In this regard, it was important that I select a couple of projects that are somewhat diverse; this maximizes my potential for learning.   

Bringing up the rear is my strange desire to give back to the communities that have supported my computing endeavors for so long.  Plenty of people use open source products without ever donating to the projects themselves, and that's not a major concern to me, it's part of the goal.  However, (for whatever strange reason) I am personally motivated to provide something in exchange for everything that I have received from the open source community.  

With those goals in mind, I had a few additional criteria.  I did not want to focus on projects that use languages/tools that were entirely foreign to me.  While that could be an interesting way to get into a language, it can also be a difficult way to get involved in a project (you're highly likely to push patches with code formatting issues, and novice implementation errors - these can cause headaches for other participants who must merge your patches).  I also wanted to support projects that are important to me (i.e., projects I use currently).  Lastly, licensing was important to me.  I'm not stringent about license, but it has to be valid open source without any strange customizations.  

Since I am currently working on several projects that involve geospatial, and I elected to use GeoServer as a mapping server mechanism, that became an initial choice.  GeoServer (GNU GPL v2) has a large and mature code-base, and is very tightly woven with several other libraries.  Geospatial is powering many new and exciting frontiers in and out of IT, and (imo) learning more about geospatial technologies is not a bad idea.  GeoServer is in Java, and they use Maven and Eclipse for building - all things with which I am familiar.  They use Subversion for source control, which I have not used in several years, so a refresher course here might be good (like it or not, I doubt that Subversion will go away for another decade or so).  They also use Atlassian products for their wiki and bug tracking, and I happen to think that Confluence (wiki) and JIRA (issue tracker) are excellent products.  

Another product I am currently using is Habari, which powers this blog.  My [first post](http://imperialwicket.com/habari-wordpress-x-1337) discusses why I like Habari.  There is a certain elegance to Habari that I love, a certain _je ne sais quoi_ that really makes it unique among the products swimming in the sea of CMS.  Habari has clean code, and it code organization is important to the project.  It also has acute attention to simplicity in its administrative pages and its directory structure.  These are factors that I appreciate, and that are not found in every open source project.  Habari is a PHP project, uses SVN and uses the Apache License.  Habari is smaller in nature than GeoServer, and has a smaller community.  Habari is also a relatively young code base (current developer release is 0.7).  

#### Taking My First Steps

After spending some time on the IRC channels (I found IRC to be a good source for Habari, and not so productive for GeoServer - although this result may be directly related to the times I was checking).  I also checked out the mailing lists, which seemed more fruitful on the GeoServer side.  So with some available lines of communication, I could move on to more concrete actions.

In my mind, my first step for GeoServer was to run through a couple of standard usage cases, and document them.  So the first thing I did was install a [geospatial stack on an EC2 instance](http://imperialwicket.com/aws-configuring-a-geo-spatial-stack-in-amazon-linux) and write it all down.  I then lucked out a bit.  I got an intro from [Mel](http://blog.melchua.com/about/), who linked me to a couple of members of the [OpenGeo](http://opengeo.org/about/) team.  Through that intro I was able to get a pleasant welcoming and a bit of encouragement to get involved with GeoServer, and I also got a tip about some quick bug fixes that would be welcome on the [GeoNode](http://dev.geonode.org/) project.  GeoNode is an additional layer in the geospatial stack that offers geospatial functionalities for Django.  While I was explicitly trying not to get involved with projects that required substantial language learning, I have secretly been looking for an impetus to enhance my (very beginner) Python knowledge.  As such, I am probably going to break my own requirement, and take the long road to Geoserver.  It may not help me with my explicit resolution (to submit code/docs/bugs/support/etc. to GeoServer during 2011), but it will get me to brush up on my Python, redo some Django tutorials, and hit the GeoNode bug tracker - and that's certainly not a bad thing.  Had I not received a helpful introduction, I would have hit the developer mailing list for GeoServer with a note about my interest in participation, some details about my areas of interest and experience, and any other information about how I think I can help the project.  

For Habari I am going to go a slightly different route.  My initial intent was to start with themes and plugins.  After all, these efforts would give me a good opportunity to review and familiarize myself with the Habari code, while creating a tangible result.  While I still plan to go through with this effort, I also had a bit of luck on the Habari front, and I got some comments on my [resolution post](http://imperialwicket.com/new-years-resolution-more-open-source-participation) from an active Habari contributor.  When he highlighted that there are a lot different ways to participate in open source projects, I decided to note that I exactly what I was planning to do to help (working on themes/plugins, and also documentation updates).  It seems that documentation updates would be welcome, so I will likely go that route for my first pass.  I will keep the themes and plugins in mind, but they are more for personal use than as contributions to the project (not to diminish the importance of plugins/themes - they can make or break a CMS).  For Habari, if I had not received helpful comments on my blog post, I would have checked out their IRC channel, and asked how I can help.  The few questions I have asked there were welcomed and met with helpful responses.  

#### Cheaters Usually Win

They really do, it's an unfortunate truth that you have to face eventually.  I say this because I think I managed to skirt one of the more difficult passageways in getting involved with open source.  That is, the very first step - weaseling your way into the project.  To be honest though, I don't really feel like I cheated, all I did was post a blog article saying that I intended to get involved with a couple of projects, and I managed to make a few contacts within the projects as a result.  I have to admit, just posting to IRC or mailing lists, with something like, "I want to help", seems a bit intimidating (but you should still go for it!).  Luckily, I can take that step knowing that someone on the other side just might recognize my name, and push me in the right direction.  What have I learned so far?  I have some handy steps:

1.  Figure out what you want to achieve out of your open source participation
2.  Choose requirements for the projects (technologies, functional areas, licensing, etc.)
3.  Use the software (if you haven't previously)
4.  Determine how to communicate with the project participants (Mailing lists, IRC channels, or ?)
5.  Optional - write a blog post about the projects you've selected and cross your fingers
6.  Figure out where you think you fit in, and why.  Hit the communication lines with this info, and see if active participants agree.  Either get started based on your initial assessment, or offer some compromise based on feedback from active participants.

Remember that providing code is integral to open source software, but keep in mind that they also need people documenting bugs, regression testing, keeping documentation current, performing web site maintenance, supporting users in mailing lists and forums, and the list continues.  

2011 is here, now that I have my initial steps planned out and I have some helpful contacts, it's time to start working.  I'll post more about initial efforts and interactions for GeoServer (and GeoNode) bug fixes and Habari documentation updates soon.