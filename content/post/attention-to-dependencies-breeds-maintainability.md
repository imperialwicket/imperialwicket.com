{
  "title": "Attention to Dependencies Breeds Maintainability",
  "description": "Ease of maintenance _is_ a feature, but attention to dependencies is what provides that feature for your software project.",
  "date": "2024-11-12",
  "url": "attention-to-dependencies-breeds-maintainability/",
  "type": "post",
  "tags": ["ci/cd"],
  "draft": false
}

I recently read [Ease of maintenance is a feature](https://ronakgothi.com/p/ease-of-maintenance-is-a-feature/) along with some [HN commentary](https://news.ycombinator.com/item?id=42087147). I walked away from this nodding emphatically along with the concept that _ease of maintenance is a feature_, and choosing sides among the crowd who are for or against a particular implementation or language. I also walked away a little angry that Ronak pointed out something important, gave some candidate assessment tooling, but didn't provide instructions for actually _developing_ this 'feature.' While I'm not going to solve the entire problem for you in this post, I am going to attempt to convince you that dependency management is critical to ease of maintenance.

You should read [the original post](https://ronakgothi.com/p/ease-of-maintenance-is-a-feature/) (it's short; I'll wait). If you can't be bothered: the duration from _getting code_ through _seeing a live update_ (presumably in production) is the proxy for ease of maintenance. I read comments suggesting that _how long it takes to setup a new computer_ or _how long it takes to onboard a new team member_ are similar measuring tools for _ease of maintenance_. Ronak implies that ease of maintenance ensures software is future-proofed against many models of ownership change (team size reduction, team member attrition, prioritization challenges, skills/resourcing). There's also a closing point that _Software should be built to last forever,_ which strikes me as a bit optimistic, but it's a nice north star concept. 

## Dependency mis-management (or, works on my machine)

Modern tooling does a great job of abstracting away _most_ of our dependency concerns. `pip`, `bundler`, `composer`, `maven`, `yarn`, `npm`, `bun`, `pnpm`, etc. coupled with containers or virtual machines can often get a project up and running with very little friction. This same tooling often does a great job of *allowing us to cheat at repeatability* once we're initially setup and functioning. This is where a young project can quickly start down the slippery slope of _only-works-on-my-machine_ (hopefully also in production, but no one will know _why_).

For clarity, I consider a "Dependency" to be anything that your software requires to function. If there's a failure mode for your software, and it's not caused by the code itself, then it's being caused by some dependency. 

Getting back to _ease of maintenance_, I think Ronak's 2, 3, and 5 (setup/deps, boot locally, smoke test) are all part of good dependency management. I also think that 1, 4, and 6 (clone repo, make code change, push code change) are highly unlikely to make a big difference in _ease of maintenance_, hence my strong focus on dependencies. (If the biggest problem for maintenance is 4 - actually making a code change, we have an entirely different challenge on our hands.)

If you're a solo developer, *works on my machine* might be all that really matters. I want to call this out to indicate there's a spectrum at play here, but it's also worth noting that we're not really talking about the solo developer case. As soon as you have a handful of people, dependency issues usually start to show up. You can address them with standardization and guard rails or you can ~label them "user error," or "skill issue," or just point out that it works on your machine~ not address them.

## Dependency Management

Assuming we have at least a few people working on a software project, once things are in production it's important to cultivate _ease of maintenance_. Since I'm saying that dependency management is critical to ease of maintenance, I'll go one obvious step further and say that the key link between dependency management and ease of maintenance is the elusive _repeatable build_. And now some of you might be picking up pitch forks and yelling that package managers give you repeatable builds! And once the chaos dies down we can talk about version pinning and download times and caching and the love for "latest."

I'm happy to concede that _often_ the distinction between a latest package configuration and a pinned package is not very important. I'm also aware that using the latest package is _often_ better from a security/vulnerabilty management perspective. But, when _ease of maintenance_ is a key feature of your software project, updating dependencies is easier and better quarantined. While you must more actively complete some of this work, in my experience it's a nice balance for the confidence and reliability you get in exchange for manual dependency updates.

### Language-specific dependencies

Think like a CI or package manager cache, and then do the cache's job for it (and probably use CI to do it!). If developer A did all the work to figure out the dependencies that make the code work, trust (but verify) developer A! When developer A commits a change to `<package-config-file>` run your tests and if they pass, stash an archive of that explicit dependency set for future use. Developers B, C, D, ... , N just download an archive (one archive, as opposed to one archive per dependency) and extract it, AND they have a much higher degree of confidence that it all works like the last release. I quite like `$(shasum <package-config-file>).tar.gz`, but there are a few ways to do this. Just don't use the commit hash, because that will assume that new dependencies come about with each commit, and that shouldn't be the case. 

#### But I run `<some-package-manager>` in prod!? 
Not anymore, you don't need to! Security will love it and far fewer package bugs or package config split-brains will happen in prod. Now you can tell prod to just fetch the language specific dependencies from that dependency 'build' you generate whenever the package config file changes.

#### But I need to change packages sometimes!? 
Correct, and now you have a clean starting place for both the developer who needs the dependencies to **just work** or for the developer who wants to be in a working state so that they can add/update dependencies. Bonus - your dependency changes don't actually go live to other developers until they pass CI. No more asking around for who broke the dependencies, if they get broken you'll know right away and it shouldn't affect anyone else (they can all still get the old package set). 

### Language (itself) dependencies

I personally like native package managers for controlling language installations, but there are a lot of language-specific tools that will also solve this challenge effectively. The absolute requirement here is that a developer shouldn't be able to run your software on the wrong language version unless they're intentionally doing so. Some options:

 - language package manager (nvm, pyenv, etc.)
 - specific containers
 - custom vm images
 - bootstrap scripts
 - config scripts

This often requires some shell/wrapper scripting, and it's easy to shrug it off as not strictly necessary. While this is true, it's not **strictly** necessary, resist the temptation to skip this, and make a script to ensure consistency guarantees here for your fellow software developers.

### Diagnostics and troubleshooting

IFF you're using a bootstrap script, you can easily expand it to double as your diagnostic script. If you aren't using a bootstrap script, you should make a diagnostic script. The purpose is to help the team identify what's unique on the system when things aren't working as expected. The bigger your team gets the more this is addressed by a workstation management solution or IT department, but until then, it's worth the effort to write a script that spits out all the dependencies on the system so that you can quickly identify inconsistencies and resolve them. 

Related to this is the concept that "pick your own device/workstation/OS" is both a blessing and a curse. If everyone develops on the same OS and the software ships to a single OS, everything is easier. Whatever you choose, you need to support all the operating systems that your developers use. You can do that via containers, vms, host machine config, whatever - but don't make second-tier developer machines/OSes a problem.

### Infrastructure dependencies

DNS? Networking? Cloud dependencies? External services? 

Infra dependency management can go a lot of ways, but we can distill this similarly to the Language dependencies. You need to make sure that the software reliably works. That could be docker-compose, vagrant, chef/ansible/puppet code, developer specific cloud environments (maybe even a terraform/cloudformation repo), or even host machine configuration. But it has to be controlled and whatever is controlling it needs to be referenced by or stored in your software repository.

There also shouldn't be multiple ways to run the software. If I'm developing on a supported OS, I shouldn't have the option to use virtual machines OR docker. I shouldn't be able to test everything but Billing with local containers, but need to stand up a cloud environment for testing a Billing change. Pick a solution and figure out how to make it work. Once there are two solutions, things are starting to lose track of their _ease of maintenance_.

## But, How?

Above is a lengthy list that proves _There's More Than One Way To Do It_. What you need to do is choose the way that your software project is going to do it, implement that way, and then keep everyone aligned on the current implementation. Then, although there's more than one way to do it, there's only one way to do it *in this repository*.

 - No, you can't use `yarn`, we use `npm`.
 - No, you can't launch a new service that's configured with `chef`, our other services are configured with `ansible`.
 - No, your same-repo code can't use $otherPythonVersion (unless you upgrade everything).

Change can happen, but changes to these implentations _must be global_.

### The Elephant

I didn't talk about documentation. I think we all know that none of this works without at least a little bit of it. Write docs. Write them for other people (including future-you). Make sure they're useful. A document that explains how to do a convoluted task isn't as useful as fixing the task and writing a simpler instruction document.

### Really, How?

Take the burden of choice away. Managing dependencies happens in a chosen way, and no other options are allowed. Changing a dependency _must happen in code_, and it really should only happen when a human requests or specifically approves it.

When someone says that their development environment is messed up, it should be completely appropriate to say "re-clone the repo." If troubleshooting your developer environment is easier than just starting a clean repository clone and local environment, your _ease of maintenance_ is already broken - and you probably need to clean up your dependency management.
