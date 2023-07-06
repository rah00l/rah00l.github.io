---
layout: post
title: Guide to Installing Sublime Text 3 on Ubuntu
tags: [Sublime]
categories: Installation
---

Sublime Text is a highly advanced text editor designed for coding, markup, and writing.

To install Sublime Text using apt-get, follow these steps:

Open the terminal and enter the following command:

```console
sudo add-apt-repository ppa:webupd8team/sublime-text-3
```

Update the package list by running:
```console
sudo apt-get update
```

Install Sublime Text by executing the command:
```console
sudo apt-get install sublime-text-installer
```

If you want to uninstall Sublime Text, use the following command:

```console
sudo apt-get remove sublime-text-installer
```

To install Sublime Text from a .deb package downloaded directly from the Sublime Text website [http://www.sublimetext.com/3](http://www.sublimetext.com/3) or installed using the Ubuntu Software Center, use the following command:

```console
sudo dpkg -i sublime-text_build-3047_amd64.deb
```
These instructions cover the installation process for Sublime Text 3 on Ubuntu.