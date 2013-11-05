---
layout: post
title: Configuring Office Communicator in Pidgin
tags: Pidgin SIPE
author: Marcelo Moreira
excerpt: Configuring Office Communicator in Pidgin
---

If you're on Ubuntu, and you need to connect to MS Lync (formerly known as Office Communicator), install the SIPE plugin for pidgin:

{% highlight console %}
sudo apt-get install pidgin-sipe
{% endhighlight %}

When you configure you account, dont forget to set the User Agent to: `UCCAPI/4.0.7577.314 OC/4.0.7577.314 (Microsoft Lync 2010)`

If you get the infamous ["Read error"](https://bugs.launchpad.net/ubuntu/+source/pidgin/+bug/950790) when you try to connect, then do the following:

*    Create a file named /usr/local/bin/pidgin-workaround
     {% highlight console %}
     vim /usr/local/bin/pidgin-workaround
     {% endhighlight %}
*    Put the following content inside:
     {% highlight console %}
     #!/bin/sh
     NSS_SSL_CBC_RANDOM_IV=0 exec /usr/bin/pidgin "$@"
     {% endhighlight %}
*    make sure it is executable
     {% highlight console %}
     chmod a+x /usr/local/bin/pidgin-workaround
     {% endhighlight %}

Now, instead of launching the pidgin executable, launch this script and everything will work out fine.

I later found out that the piggin launcher in the Indicator Plugin does not work, because it still uses the old executable.

Alternatively, you can set the option `NSS_SSL_CBC_RANDOM_IV=0` directly under `/etc/environment`.
