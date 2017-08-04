---
layout: post
title: Difference between Git Pull vs Rebase
tags: [github ]
categories:
---

### Git Pull: `git pull = git fetch + git merge`
Git pull will pull down from a remote whatever you ask (so, whatever trunk you’re asking for) and instantly merge it into the branch you’re in when you make the request. Pull is a high-level request that runs ‘fetch’ then a ‘merge’ by default.

Using `git pull` will fetch any changes from the remote branch and merge them on to your local branch, creating a new merge commit.

If you follow the instructions in the Git message and pull:

	git pull origin master

Git actually does the following two commands:

	git fetch origin master
	git merge origin/master

And the following happens to the commit tree:

	- C1 - C2 - C3 - C4 - C5 - C6 - C7 (master)
                  \               \
                   C5' - C6' - C7' - C8 (origin/master)

### Git Rebase: `git fetch + git rebase`
Finally, git rebase is pretty cool. Anything you’ve changed by committing to your current branch but are no in the upstream are saved to a temporary area, so your branch is the same as it was before you started your changes, i.e. clean. If you do **‘git rebase’, git will pull down the remote changes, rewind your local branch, then replays all your changes over the top of your current branch one by one**, until you’re all up to date.

On the other hand, if you are to rebase:

	git fetch origin master
	git rebase origin

The following happens to the commit tree:

	- C1 - C2 - C3 - C4 - C5' - C6' - C7' - C5 - C6 - C7 (master)
                                  |
                                  (origin/master)

### Conclusion

* If you are a git beginner and you want things to be safe, then using git pull and  git merge all the time for merging code. Of course, it would create trouble if you want to debug based on git history, but it is still the safest way.
* If you want to maintain a clean and tidy git history, git rebase is for you.
