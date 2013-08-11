Title: So... What Exactly is DevOps ?!
Tags: devops management
Author: Marcelo Moreira
Summary: What is DevOps ? (Second out of three articles in the "Beginning of DevOps" series)

I have come across this article from O’Reilly, but only recently was able to finally read it. The article is entitled “What is DevOps?” and can be found [here](http://radar.oreilly.com/2012/06/what-is-devops.html).

I confess that I enjoyed very much that reading. It is succinct and direct to the point. It is also very concise and well put.

I particularly like how the author ([Loudikes](http://radar.oreilly.com/mikel)) starts by defining what was operations in the past, and then transition to traditional operations, to finally dig into “infrastructure as a code”.

To quote his article:

> If you’re going to do operations reliably, you need to make it reproducible and programmatic. Hence virtual machines to shield software from configuration issues. Hence [Puppet](https://puppetlabs.com/) and [Chef](http://www.opscode.com/chef/) to automate configuration, so you know every machine has an identical software configuration and is running the right services. Hence [Vagrant](http://www.vagrantup.com/) to ensure that all your virtual machines are constructed identically from the start. Hence automated monitoring tools to ensure that your clusters are running properly. It doesn’t matter whether the nodes are in your own data center, in a hosting facility, or in a public cloud. If you’re not writing software to manage them, you’re not surviving.

He continues...

> Furthermore, as we move further and further away from traditional hardware servers and networks, and into a world that’s virtualized on every level, old-style system administration ceases to work. Physical machines in a physical machine room won’t disappear, but they’re no longer the only thing a system administrator has to worry about.

And then concludes:

> So infrastructure had to become code. [...] It didn’t take long for leading-edge sysadmins to realize that handcrafted configurations and non-reproducible incantations were a bad way to run their shops. It’s possible that this trend means the end of traditional system administrators, whose jobs are reduced to racking up systems for Amazon or Rackspace. But that’s only likely to be the fate of those sysadmins who refuse to grow and adapt as the computing industry evolves.

> Good sysadmins have always realized that automation was a significant component of their job and will adapt as automation becomes even more important. The new sysadmin won’t power down a machine, replace a failing disk drive, reboot, and restore from backup; he’ll write software to detect a misbehaving EC2 instance automatically, destroy the bad instance, spin up a new one, and configure it, all without interrupting service. With automation at this level, the new “ops guy” won’t care if he’s responsible for a dozen systems or 10,000.

> [..] Success isn’t based entirely on integrating operations into development. It’s naive to think that even the best development groups, aware of the challenges of high-performance, distributed applications, can write software that won’t fail. On this two-way street, do developers wear the beepers, or IT staff? [..] it’s important not to divorce developers from the consequences of their work since the fires are frequently set by their code. So, both developers and operations carry the beepers. Sharing responsibilities has another benefit. Rather than finger-pointing post-mortems that try to figure out whether an outage was caused by bad code or operational errors, when operations and development teams work together to solve outages,a post-mortem can focus less on assigning blame than on making systems more resilient in the future.

What such a beautiful and inspiring article to read !

Now, what about getting this in the workplace ? Well... This is a completely different beast, and I will leave this talk to a later time.

