---
layout: post
title: Install github version control systems on ubuntu
tags: [Github]
categories: Installation
---

**Github** is a distributed version control system.

Running
	*sudo apt-get update* simply makes sure your list of packages from all repositories and PPA's is up to date.

	sudo apt-get update

### Install Git with Apt

One of the most popular **version control systems** is **git**, a distributed version control system.

	sudo apt-get install git

**Git GUI** is a cross-platform and portable Tcl/Tk based GUI front-end for Git that works on Linux, Windows and Mac OS X.

	sudo apt-get install git-gui

**Gitg** is a GNOME GUI front-end to view Git repositories.

	sudo apt-get install git-g

**Bonus points**

* Add git branch name to bash prompt

	$ gedit ~/.bashrc

{% highlight bash %}
#Git branch in prompt.
parse_git_branch() {
git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}
export PS1="\u@\h \W\[\033[32m\]\$(parse_git_branch)\[\033[00m\] $"`
{% endhighlight %}

* Set user name and email configurations for GIT

{% highlight bash %}
git config --list
git config --global user.name "firstname lastname"
git config --global user.email "example@gmail.com"
{% endhighlight %}

We have covered about how to install github a distributed version control system on Ubuntu.
