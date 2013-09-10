---
layout: post
title: Generating Your Own Keypairs for AWS
tags: Encryption
author: Marcelo Moreira
excerpt: Generating Your Own Keypairs for AWS
---

Whenever you launch and intance at AWS, you need to bing it to a keypair. This is the oly way to log in to it.

I like to retain control of the keys mysef, so I preer to generate them on my own, instead of letting Amazon do that for me.

I believe from a security standpoint, there is nothing really to worry here. Amazon's method is perfectly fine, and will work flawless for almost everyone.

Here is what you need to do:

{% highlight console %}
ssh-keygen -b 4096 -C "YOUREMAIL@WHATEVER.COM" -f YOURFILENAMEHERE
{% endhighlight %}

You will get 2 files:

{% highlight console %}
$ ls -la
-rw-------  1 marcelom operator 3326 Sep  9 12:43 YOURFILENAMEHERE
-rw-------  1 marcelom operator  745 Sep  9 12:43 YOURFILENAMEHERE.pub
{% endhighlight %}

Also, I HIGHLY recommend that you rotect your private key with a passphrase. It will give you much ore ease of mind in case it leaks out. Plus, it kinds of work as a 2-factor uthentication thing... The private key is the "somehing that you have", and the passhrase is the "something that you know".
