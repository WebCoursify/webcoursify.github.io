---
layout: page
title: Introduction to Git
tagline: 
tags: 
modified: 01-22-2015
comments: true
---

[Git](http://git-scm.com/) is perhaps the most popular version control tool for software developers now. Compare to svn, it takes a little more time to learn, but more powerful, and who has learnt the basic usage will find it actually easier and more convenient to use :) Here we'll provided a tutorial about the basic usage of git, which covers most of the functionalities we need during daily developments. 

### Git is distributed

In svn, there's a central repository, everyone will need to sync with this central repository, and commit to it. However, git is distributed: a repository can be locally, but also can be on the remote server. One can make changes to his local repository without changing the repository on the remote server, thus won't affect other people who're using the same remote repo. He can choose to fetch down the changes on the remote repo and merge into his local repo, or push the changes in his local repo to the remote repo to share with others, at any time he wants. 

What's more, git support branches, both locally and remotely. The default branch is **master** branch, but everyone can create or delete branches as they want. One can switch between different branches in the local repo very easily to develop different features in different branches, and push the changes to the corresponding branches in the remote. 

### Create a Repo

There're two ways to create to repo to start to work on.

If there's an existed git repo, and you want to start your work from that, you can use <code>checkout</code>

{% highlight bash %}
git checkout https://github.com/WebCoursify/webcoursify.github.io.git
{% endhighlight %}

Or, if you want to create a brand new repo, you can use <code>init</code>

{% highlight bash %}
cd {the directory you want to create the repo, doesn't match empty or not}
git init
{% endhighlight %}

### Save file changes

In git, the way to make your changes persistent is to **commit** the changes. To do this, you'll first need to **add** the files you have changed and want to save:

{% highlight %}
git add {filepath} # add a file or a directory recursively
git rm  {filepath} # tell git you want to remove this file
git add .          # add all changes you make within the current directory and the subdirectories
{% endhighlight %}

You can use <code>git status</code> to check the local repo status:

git status

