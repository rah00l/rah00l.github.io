---
layout: post
title: Manage multiple GitHub accounts on a single machine
tags: [Github]
categories: Github
style: fill
color: secondary
---

Most developers work life needs to manage multiple GitHub accounts on the same machine. Also, It could simply be a desire to have separate GitHub accounts for work-related projects and personal projects.

<img src="{{ site.baseurl }}/public/images/manage-multiple-gitHub-accounts.png"/>

Let's talk, How to manage multiple Github accounts from one computer?

### Manage multiple GitHub accounts on a single machine with SSH keys

#### Contents:
- Set up SSH Keys
- Add SSH keys to GitHub account
- Create configuration files to manage keys
- Update stored identities
- Test PUSH to new repo
- Test PUSH to existing repo

What you need?

- Two active GitHub accounts
- A Unix based machine
- Some knowledge of working with the terminal

#### Set up SSH Keys:
Open your computer terminal. Proceed and follow the steps below to create and save two separate keys.

```console
$ cd ~/.ssh
$ ssh-keygen -t rsa -b 4096 -C "rahulpatil2387@gmail.com"
  # save as id_rsa_personal
$ ssh-keygen -t rsa -b 4096 -C "rahul.patil@office.com"
  # save as id_rsa_work
```

Running the above commands will create four (4) files in the .ssh directory in your local machine:

```js
id_rsa_personal
id_rsa_personal.pub
id_rsa_work
id_rsa_work.pub
```

The files with the .pub extension are the public files that you would add to your GitHub account.

#### Add SSH keys to GitHub account:

Copy one of the keys you created, say, the personal key by running the command below:

```console
$ pbcopy < ~/.ssh/id_rsa_personal.pub
```
Log in and follow the steps below to add the newly created key to your GitHub account.

- Go to Settings under your Github account,
- Click on the SSH and GPG keys link 
- Click on the green New SSH key button
- Add a title to identify the key you are about to paste 
- Paste the personal key in the key input field provided. Click on Add SSH key and confirm your GitHub password when prompted.

Go through the same steps to add your work SSH key.

Create configuration files to manage keys

Go back to the .ssh folder in your terminal and create a configuration file for your keys using the command below:

```console
$ touch config
```

Open and edit the file using nano/vim. nano: `$ nano config`

```
 #github work account
Host work
        HostName github.com
        User git
        IdentityFile ~/.ssh/id_rsa_work

 #github personal account
Host personal
        HostName github.com
        User git
        IdentityFile ~/.ssh/id_rsa_personal
```

Save and exit.

#### Update stored identities:

Clear currently stored identities:

```console
$ ssh-add -D
```

Add new keys:

```console
$ ssh-add id_rsa_personal
Identity added: id_rsa_personal (id_rsa_personal)
```

```console
$ ssh-add id_rsa_work
Identity added: id_rsa_work (id_rsa_work)
```

Test to make sure new keys are stored:

```console
$ ssh-add -l
2048 SHA256:xBxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx id_rsa_personal (RSA)
2048 SHA256:wXxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx id_rsa_work (RSA)
```

Test to make sure Github recognizes the keys:

```console
$ ssh -T personal
Hi rah00l! You've successfully authenticated, but GitHub does not provide shell access.
```


```console
$ ssh -T work
Hi rahul-work! You've successfully authenticated, but GitHub does not provide shell access.
```

#### Test PUSH with new repo:
On Github, create a new repo in your personal account, called test-personal.

Back on your local machine, create a test directory:

```console
$ cd ~/documents
$ mkdir test-personal
$ cd test-personal
```
Just to add a blank “readme.md” file and PUSH to Github:

```console
$ touch readme.md
$ git init
$ git add .
$ git commit -am "first commit"
$ git remote add origin git@personal:rah00l/test-personal.git
$ git push origin master
```
> Notice how we’re using the custom account, *git@personal*, instead of *git@github.com*.


#### Test PUSH with existing repo:

```console
cd ~/your project
```
Modify a pre-existing repository,

To switch a pre-existing repository to use SSH instead of HTTPS, you can change the remote url within your .git/config file.

```
[remote "origin"]
    fetch = +refs/heads/*:refs/remotes/origin/*
    -url = https://github.com/rah00l/rah00l.github.io.git
    +url = git@personal:rah00l/rah00l.github.io.git
```
A shortcut is to use the set-url command:

```
$ git remote set-url origin git@personal:rah00l/rah00l.github.io.git
```
Now push and test at your personal account.

Repeat the process for your githubWork account.