---
layout: post
title: Exporting & Importing a Git Repo
tags: git migration import export
author: Marcelo Moreira
excerpt: Migrating a git repo
---

Have you ever needed to fully migrate (export and import) a git repo from one central location to another ?

Here is a step-by-step guide that works !

Before you start, make sure you know both clone URLs for the repos you are trying to export/import.

For example, to import it on GitHub, make sure that the repository you're trying to push to already exist on GitHub and is empty.

## Step-By-step

1. Make a "bare" clone of the repository (i.e. a full copy of the data, but without a working directory for editing files) using the external clone URL. This ensures a clean, fresh export of all the old data.
2. Push the local cloned repository to GitHub using the "mirror" option, which ensures that all references (i.e. branches, tags, etc.) are copied to the imported repository.

Here are all the commands written out:

{% highlight console %}
# In this example, we use an external account named extuser and
# a GitHub account named ghuser to transfer repo.git

git clone --bare https://githost.org/extuser/repo.git
# Make a bare clone of the external repository to a local directory

cd repo.git
git push --mirror https://github.com/ghuser/repo.git
# Push mirror to new GitHub repository

cd ..
rm -rf repo.git
# Remove temporary local repository
{% endhighlight %}

Your newly imported repository should be ready to go on GitHub!
