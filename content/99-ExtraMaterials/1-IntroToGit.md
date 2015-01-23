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
>> git checkout {url or path to the remote repo like, https://github.com/WebCoursify/webcoursify.github.io.git}
{% endhighlight %}

Or, if you want to create a brand new repo, you can use <code>init</code>

{% highlight bash %}
>> cd [the directory you want to create the repo, doesn't match empty or not]
>> git init
{% endhighlight %}

Now you have a working repo locally.

### Save file changes

In git, the way to make your changes persistent is to **commit** the changes. To do this, you'll first need to **add** the files you have changed and want to save:

{% highlight bash %}
# add a file or a directory recursively
>> git add {filepath} 
# tell git you want to remove this file, and this does actually remove it
>> git rm  {filepath} 
# add all changes you make within the current directory and the subdirectories
>> git add .          
{% endhighlight %}

You can use <code>git status</code> to check the local repo status. For example, at this very moment when I'm writting this post, I'll see:

{% highlight bash %}
>> git status

On branch master
Your branch is up-to-date with 'origin/master'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   1-IntroToGit.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	index.md
{% endhighlight %}

I just use <code>git add</code> to add the file "1-IntroToGit.md". Now, I want to permanently (actually not really, because we can always rollback...) save the change, I could just:

{% highlight bash %}
>> git commit -m "just doing a demo"
{% endhighlight %}

Now when I type <code>git status</code>:

{% highlight bash %}
>> git status

On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	index.md

nothing added to commit but untracked files present (use "git add" to track)
{% endhighlight %}

We can use <code>git log</code> to check the history of our modifications:

{% highlight bash %}
>> git log

commit xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx # This is the commit id
Author: XXX <xxx@xxx>
Date:   Thu Jan 22 20:49:50 2015 -0500

    just a demo

...
{% endhighlight %}

By checking the log we can rollback to a previous point. For example, we want to go back to the point of a specific commit, just find its commit id, then we can:

{% highlight bash %}
>> git reset --soft {commit id}
{% endhighlight %}

And then you can undo the things you regret. Magic! For more details about reverting and rolling back, please refer to other online resources like [this one](https://www.atlassian.com/git/tutorials/undoing-changes) cuz we'd like to kepp this tutorial simple. 

### Playing with Branch

Branch in git is extremely useful. One example is we can develop and test feature A on branch one, develop and test feature B on branch two, and A and B won't interfere with each other until the moment they're finished and merged into the same branch. 

Here's how to create a branch

{% highlight bash %}
>> git checkout -b [branch name]
{% endhighlight %}

Switch to a branch

{% highlight bash %}
>> git checkout [branch name]
{% endhighlight %}

Merge branch B into branch A

{% highlight bash %}
>> git checkout A
>> git merge B
{% endhighlight %}

Another approach is:

{% highlight bash %}
>> git checkout A
>> git rebase B
{% endhighlight %}

There's a great [post](https://www.atlassian.com/git/tutorials/merging-vs-rebasing/conceptual-overview) explaining the differences. Both approaches are used a lot. 

And of course, we can delete a branch:

{% highlight bash %}
>> git checkout -d [branch name]
{% endhighlight %}

### Sync with Remote Repo

In git, one can make a local repo keep track of multiple remote repos (which are identified by different names) at the same time, but we only need one in most of the time. 

The default name for the first remote repo will be ***origin***. If you use <code>git checkout</code> to create a local repo, then it's already tracking the remote repo you checkout from as the ***origin***. If not, you need to add a remote repo first. Do this by:

{% highlight bash %}
>> git remote add origin {url or path to the repo}
{% endhighlight %}

Now, the way to fetch remote changes is:

{% highlight bash %}
>> git fetch origin 
{% endhighlight %}

Now, for example, if there're updates on the master branch of this remote repo, you can merge these changes into your local master branch by:

{% highlight bash %}
>> git checkout master
>> git merge origin/master
{% endhighlight %}

Or, you can just use <code>git pull</code>, which is just first fetch then merge. 

Now you want to push the saved changes on your local master branch to the remote master branch (remember you need to commit the changes you want to save first), just:

{% highlight bash %}
>> git checkout master
>> git push origin master
{% endhighlight %}





