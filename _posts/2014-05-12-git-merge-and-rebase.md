---
layout: post
title: Git Merge and Rebase
tags: [github ]
categories:
---

>Merge and Rebase are two strategies available in Git to combine two ( or more) branches into one branch.

Let’s say we have two branches `feature1` and `feature2` that have diverged from a common
commit “a” to have four commits each.


<img src="{{ site.url }}/public/images/git-two-branches.png" />

### Git Merge:
Let's say that you have two branches in your Git repo viz. feature1 and feature2, where the feature2 branch is being used to develop something additional & feature1 branch is your production quality code branch or main branch.

After you are done developing the feature2 branch, you want to combine it with the feature1
branch.

If you `git merge` it into feature1, it will combine the tasks of feature2 into feature1 by creating a `single new commit` "b" at the tip of feature1.

{% highlight sh %}
on feature1 branch>git checkout feature2
.................... make the development
.................... add commits
................... >git checkout feature1
................... >git merge feature2
{% endhighlight %}

<img src="{{ site.url }}/public/images/git-merge.png" />

**"Git Merge is much like taking two threads and tying them up in a knot."**


### Git Rebase:
Git rebase will place the entire commit history of the feature2 branch on the tip of feature1. It means that your entire feature2 branch will be reattached to the tip of feature1 and it will look like a linear sequence of commits.

{% highlight sh %}
on feature1 branch>git checkout feature2
.................... make the development
.................... add commits
................... >git checkout feature1
................... >git rebase origin/feature2
{% endhighlight %}



<img src="{{ site.url }}/public/images/git-rebase.png" />

**"Git Rebase is used to make the branching paths in history cleaner and repository structure linear."**

### Conclusion:

* When multiple developers work on a shared branch, use pull & rebase your outgoing commits, to keep history cleaner.

* To re-integrate a completed feature branch, use merge.



