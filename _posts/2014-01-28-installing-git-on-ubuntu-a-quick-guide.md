---
layout: post
title: Installing Git on Ubuntu A Quick Guide
tags: [Github]
categories: Installation
style: fill
color: secondary
---

GitHub is a powerful distributed version control system that enables efficient collaboration on software development projects. To get started with Git on Ubuntu, follow these simple steps:

##### 1. Updating Package Lists:

Before proceeding, ensure your package lists and repositories are up to date. Execute the following command:

```console
sudo apt-get update
```

##### 2. Installing Git:

Git is one of the most widely used version control systems. Install it using the following command:

```console
sudo apt-get install git
```

##### 3. Git GUI Front-End:

If you prefer a graphical user interface (GUI) for Git, you can install Git GUI, a cross-platform and portable Tcl/Tk based front-end. Run the command:

```console
sudo apt-get install git-g
```

Bonus Tips:

Adding Git Branch to Bash Prompt:
Edit the .bashrc file using a text editor such as Gedit:
```console
$ gedit ~/.bashrc
```

* Add the following lines to display the current Git branch in your prompt:

{% highlight bash %}
#Git branch in prompt.
parse_git_branch() {
git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}
export PS1="\u@\h \W\[\033[32m\]\$(parse_git_branch)\[\033[00m\] $"
{% endhighlight %}


* Configuring User Name and Email:
Check your Git configuration using:

```console
git config --list
```

Set your global user name and email for Git with the following commands:
{% highlight bash %}
git config --list
git config --global user.name "firstname lastname"
git config --global user.email "example@gmail.com"
{% endhighlight %}

This guide provides a comprehensive overview of installing and configuring Git, the distributed version control system, on Ubuntu. With Git, you can enhance your software development workflow and collaborate effectively with teammates.

