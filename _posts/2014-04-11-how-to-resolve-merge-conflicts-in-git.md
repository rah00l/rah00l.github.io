---
layout: post
title: How to resolve merge-conflicts in git
tags: [Github ]
categories: Github
---


* Identify which files are in conflict (Git should tell you this).


{% highlight html %}
<html>
  <head>
<<<<<<< HEAD
    <link type="text/css" rel="stylesheet" media="all" href="style.css" />
=======
    <!-- no style -->
>>>>>>> master
  </head>
  <body>
    <h1>Hello,World! Life is great!</h1>
  </body>
</html>
{% endhighlight %}

When you receive CONFLICT, your current branch got changed with (no branch).

Try: `git mergetool`

The command doesn't necessarily open a GUI unless you install one. Running `git mergetool` for me resulted in `meld` being used.

You may install one of the following tools to use it instead:
`meld`, `opendiff`, `kdiff3`, `tkdiff`, `xxdiff`, `tortoisemerge`, `gvimdiff`, `diffuse`, `ecmerge`, `p4merge`, `araxis`, `vimdiff`, `emerge`.

It opens a GUI that steps you through each conflict, and you get to choose how to merge. Sometimes it requires a bit of `hand editing afterwards`, but usually it's enough by itself. It is much better than doing the whole thing by hand certainly.

* You may need to discuss it with fellow developers who committed the code. Once you've resolved the conflict in a file `git add the_file`.

* Followed by, `git rebase --continue` (This command will continue with rebase code with local code .. and retrive back on track like (no branch) --> current_branch.) or whatever command Git said to do when you completed.

Then you can easily push your changes to remote branch.

	git push origin current_branch
