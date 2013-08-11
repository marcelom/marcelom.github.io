Title: Welcome To My New Site
Tags: Python, Pelican
Author: Marcelo Moreira
Summary: Welcome to my new site !

Welcome to the new Marcelo@Stanford site !!!

I have finally migrated from my old and outdated Wiki site.

It was good while it lasted but there were so many problems there...

## What happened to the old site ?

The old site was a Wiki, based on the fantastic [DokuWiki](https://www.dokuwiki.org) software. I immensely thank the authors for sharing and making it so available.

This is what happened:

 1. Indeed, a Wiki has a great collaborative aspect. But that was simply not being taken advantage of. It was only me making the changes anyways...
 2. I needed to do some major rework to the URL space using Apache rewrite rules to get rid of the weird cgi-bin naming that Stanford imposed in the web pages. I was constantly tumbling there.
 3. It simply felt into a big void: no updates were being made anymore (I have no one else to blame other than myself)

## Why I made a new site ?

There were several reasons for that. They are (not in any specific order):

 1. I wanted to play with static content generators: I really do like the idea - you create content based on a markdown language, and then it gets converted to a static site based on a template.
 2. I wanted something fast: Yes... something even faster than a wiki. With a static site, all content is already generated. There is no dynamic processing of any kind.
 3. I wanted to learn it: I saw [this](http://tom.preston-werner.com/2008/11/17/blogging-like-a-hacker.html) post and it inspired me. I really liked the approach and decided to play with it. Since I am not a big fan of Ruby (sorry Ruby folks...), I immediately searched for something in Python. I found [Pelican](http://getpelican.com/) !
 4. I wanted to share more easily: by being allowed to make the posts in a flexible and portable markdown language, I am future-proofing my posts. I am not particularly stuck to a specific wiki syntax. There were a few options available, but I decided to start small and go with [Markdown](http://daringfireball.net/projects/markdown/).
 5. I wanted version control: This is absolutely a must !!! No questions asked. I use git for pretty much everything I do. I use GitHub a lot as well. The idea was to integrate the commit process to an auto-deployment hook that generates the site and deploys it on demand, every time there is a new post (or change). I will provide more details on this in another post.
 6. I wanted flexibility: This is another must. I want to be able to theme my site in whatever way I want. I also want to be able to deploy it however and wherever I want. That way I retain full control and full flexibility. My posts and data are not stuck in a database somewhere else, and I don't need to limit myself to a select hand of templates with somewhat limited flexibility. Plus, with auto-deployment, I can just make a change to my git repository (only directly at GitHub, for example) and have it publish it automatically, and with all the features above.

## What to expect ?

I hope that this new model works better, and gets updated more often and does not fall back too behind like the old site.

I am in the process of converting some data from the old Wiki. It might take a while...

I am also perfecting the auto-deployment process. I will make another post about it in the future.

You can find the [source code](https://github.com/marcelom/marceloatstanford) for this site in github.

I also plan on making some improvements to the template. Maybe something based on the Stanford Modern template, like this [page](https://www.stanford.edu/services/web/design/templates/modern/html/basic-nav.html).

In the mean time, please check back often.

My most sincere thanks for listening...

~marcelo
