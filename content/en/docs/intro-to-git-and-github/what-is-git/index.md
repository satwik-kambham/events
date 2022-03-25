---
title: "What Is Git?"
description: ""
lead: ""
date: 2022-03-23T19:13:27+05:30
lastmod: 2022-03-23T19:13:27+05:30
draft: false
images: []
menu:
  docs:
    parent: ""
weight: 491
toc: true
---

So, what exactly is git?

Git is a version control system (VCS). A version control system is similar to taking backups of your code. So, at any time, you can go and find previous version of your code. This is really helpful when you have accidentally introduced a bug in your code and want to go back to the previous version where there were no bugs.

Another major thing to note is that git is also a distributed version control system. What this means is that you can have many people working together on a project without affecting each other.

Git is mainly used from the terminal but it is also integrated into many code editors and IDEs such as [Visual Studio Code](https://code.visualstudio.com/). You also use GUI clients such as [GitHub Desktop](https://desktop.github.com/).

## Installing Git

Head over to the [downloads section](https://git-scm.com/downloads) of git's official website and follow the instructions for your operating system.

## Verifying installation

To verify that git is installed, run the following command inside your terminal:

```console
git --version
```

## Configuring Git

We need to add some user information before we start using git

- Set the username using the following command. Replace `<USER_NAME>` with the name of your choice.

```console
git config --global user.name "<USER_NAME>"
```

- Set the email using the following command. Replace `<USER_EMAIL>` with the email of your choice.

```console
git config --global user.email "<USER_EMAIL>"
```

## Verify configuration

Verify the configuration by running the following command:

```console
git config --list
```

You should see your username and email like so:

```console
user.name=User Name
user.email=user-name@contoso.com
```
