---
layout: post
title: Setting up a Ruby on Rails development environment on Ubuntu
tags: [rails, github, sublime ]
categories: installation
---

We will be setting up a Ruby on Rails development environment on Ubuntu.

Some of you may choose to develop on Ubuntu Server so that your development environment matches your production server. 

Lets start,

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

Add git branch name to bash prompt

	$ gedit ~/.bashrc

	#Git branch in prompt.
	parse_git_branch() {
	git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
	}
	export PS1="\u@\h \W\[\033[32m\]\$(parse_git_branch)\[\033[00m\] $"`

Set user name and email configurations for GIT

	git config --list
	git config --global user.name "firstname lastname"
	git config --global user.email "example@gmail.com"


The next step is to **install some dependencies** for Ruby.

Required Packages:

	sudo apt-get install gawk g++ libreadline6-dev zlib1g-dev libssl-dev libyaml-dev libsqlite3-dev sqlite3 autoconf libgdbm-dev libncurses5-dev automake libtool bison libffi-dev

	sudo apt-get install imagemagick libmagickwand-dev libxslt-dev libxml2-dev install nodejs

Mysql / PostgreSQL gem **native extensions**

	sudo apt-get install libmysql-ruby libmysqlclient-dev
	sudo apt-get install libpq-dev

### Install rvm

the Ruby Version Manager

	sudo apt-get install curl
	curl -L get.rvm.io | bash -s stable
	source ~/.rvm/scripts/rvm

### Install text editor sublime-text-3

* install with **apt-get**

		sudo add-apt-repository ppa:webupd8team/sublime-text-3
		sudo apt-get update
		sudo apt-get install sublime-text-installer

	You could remove your installation:

		sudo apt-get remove sublime-text-installer

* install with **.deb**

	If you installed Sublime Text 3 from a .deb package which was downloaded directly from Sublime Text page: [http://www.sublimetext.com/3](http://www.sublimetext.com/3)

	And you have used this command for installation

		sudo dpkg -i sublime-text_build-3047_amd64.deb

	Or you just double clicked on it and Ubuntu Software Center installed it.

### Install google-chrome

Add Key:

	wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -

  Set repository:

	sudo sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list'

  Install package:

	sudo apt-get update
	sudo apt-get install google-chrome-stable

### Install mysql-server

	sudo apt-get install mysql-server

**Install phpmyadmin**

	sudo apt-get update
	sudo apt-get install phpmyadmin

	sudo service apache2 status

	sudo service apache2 restart

http://localhost/phpmyadmin (if apach2 running on port:80)

### To run Solr server

You need a **Java Runtime Environment** to run the Solr server

**Installing Java with apt-get** is easy. First, update the package index:

	sudo apt-get update

Then, check if Java is not already installed:

	java -version

	sudo apt-get install default-jre

Then install java

	sudo apt-get install default-jdk

***

We have covered the basics of how to set up a Ruby on Rails development environment on Ubuntu.

*Thanks for reading!*
