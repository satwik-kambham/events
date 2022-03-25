---
title: "Creating a Repository"
description: ""
lead: ""
date: 2022-03-23T23:20:18+05:30
lastmod: 2022-03-23T23:20:18+05:30
draft: false
images: []
menu:
  docs:
    parent: ""
weight: 493
toc: true
---

To demonstrate the use of git and github, let us create a simple website.

## Navigate to project folder

First, open up the terminal and navigate to the folder where we want to create the website.

You can go to a particular folder using the `cd` (Change Directory) command. For example:

```console
cd C:\Users\Username\Documents
```

Then create the folder for the website itself using the `mkdir` (Make Directory) command like so:

```console
mkdir MyWebsite
```

Finally use `cd MyWebsite` to go into the website folder.

## Initializing the git repository

Let us initialize the git repository so that we can track the changes. You can do this using the following command:

```console
git init
```

## Adding website files

Let us create a file called `index.html` with the following boilerplate code:

```html
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>MyWebsite</title>
  </head>
  <body>
      
  </body>
</html>
```

You can create the file by opening up the folder inside VSCode using the `code .` command and then creating the file using VSCode's GUI.

## Checking the repository's status

You can check what all changes have been made in a repository using the `git status` command. After running the command you should get an output that looks like this:

```console
On branch main

No commits yet                                                                            
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        index.html

nothing added to commit but untracked files present (use "git add" to track)
```

This is telling us to use the `git add` command to track the changes in the `index.html` file.

## Committing changes

Let us add the `index.html` to the list of files to be tracked. To do this run the following command:

```console
git add index.html
```

Now run the `git status` command again and you will get an output like this letting us know that our changes are ready to be committed.

```console
On branch main

No commits yet                                                                            
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   index.html
```

We can finally commit out changes using the `git commit` command. We also need to add a message describing what exactly we are committing. This is really helpful when you want to know what exactly you added in the future. Commit the changes with the following command:

```console
git commit -m "Added index.html"
```

Now your changes have been committed. You can verify this by running the `git status` command again which will give you the following output:

```console
On branch main
nothing to commit, working tree clean
```
