---
layout: post
title: Basic git configuration
tags: git
author: Marcelo Moreira
excerpt: How to make some basic git configurations to make life easier...
---
There are a few things that we can do to increase the usability of git.

This is my basic git config:

{% highlight shell-session %}
git config --global color.ui auto
git config --global user.email myemail@company.com
git config --global user.name Marcelo
git config --global credential.helper 'cache --timeout=3600'
{% endhighlight %}
