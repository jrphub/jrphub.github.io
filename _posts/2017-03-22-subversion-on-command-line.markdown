---
title:  "10 - Subversion on Command Line"
date:   2017-03-22 11:30:23
categories: [Development]
tags: [svn]
---
Subversion is an open source version control system. Founded in 2000 by CollabNet, Inc. Subversion is developed as a project of the **Apache** Software Foundation (http://subversion.apache.org/)

To install Subversion (aka SVN), follow this link (http://subversion.apache.org/packages.html)

To work with SVN, one usually have 3 options

1. Plugin on IDE ( Ex- Subversive plugin on Eclipse IDE)
2. GUI tool ( Ex - Tortoise SVN on windows)
3. Command Line (CLI) - A great solution while working on server that doesn't have a GUI

In this post we will discuss about using SVN on Command Line.

**Revision** - SVN refers to different versions of repository as "Revisions" and each one has a number. Unlike in Git, where revision is a changeset, in SVN it refers to a snapshot. Changeset in  SVN is the difference between two revisions.

**Checkout** - Create a working copy from the repository in the local directory

    svn checkout http://svn.example.com/projects/project1 myproject

**Update** - Bring new changes from the repository into the working copy in the current directory

    svn update

**Status** - To check the current status of working copy , showing files that are modified in the current directory or below it.

    svn status

codes to explain the status:

![status code](http://i.imgur.com/KYTyxEg.png)

**Add** - To add new file

    svn add abc.txt

**Revert** - To remove local, uncommitted changes to a file

    svn rever pqr.txt

**Commit** - Sends local changes to the repository

    svn commit -m "commiting my files" 

All M & A status code files of current and below directory, will be committed.
To commit specific files:

    svn commit -m "committing specific file" abc.txt

**Resolve** - After "svn update", the command "svn status" shows the conflicted files with status code " C". These files need to be resolved before committing.
 The conflicted files look like this:
 
![conflicted file](http://i.imgur.com/h4P5um4.png)

Resolve the conflict, remove ASCII art notations, when you are ready, run 

    svn resolve abc.txt

Then do, svn commit

**Blame** - Shows which user last changed which line in which version

    svn blame abc.txt

**Diff** - Shows the difference between two revisions

    svn diff
    svn diff http://svn.example.com/projects/stuff/trunk http://svn.example.com/projects/stuff/branches/branch1
    svn diff -r 19:22

**Log** - Shows the story of changes that have been made.

    svn log

**Copy** - To safely copy files and directories to a new location. This also copies the history of files/directories. The .svn folder gets updated with information.

    svn copy fileA fileB

**Remove** 0r **Delete** - It removes the file from the directory. File gets status "D". The file will be deleted in next commit.

    svn remove abc.txt
    svn delete abc.txt

**Rename** - Allows safe renaming of files. Old file gets status "D" and file with new name gets satus "A". The file gets committed in next commit.

    svn move abc.txtx better_name.txt

**Export** - It gives you the files from your repository project, but without .svn folder.

    svn export http://svn.example.com/projects/project1 myproject

**Info** - To give information about current working copy

    svn info

**Relocate** - Allows different remote location to be associated with an existing working copy

    svn relocate http://svn.example.com/projects http://example-svn.com/projects

Each project folder contains 3 subdirectories

1. **Trunk** - Main version of project
2. **Branches** - separate versions of the code, often used to work on a specific release or feature, allowing many commits over time and collaboration before the feature or branch is ready.
3. **Tags** - also copies, but these are not edited after they are created, Like label. To know which version of the code is currently live

Creating a new branch from the trunk of a project:

    svn copy http://svn.example.com/projects/project1/trunk http://svn.example.com/projects/project1/branches/new-feature

Similarly tag can be created

To set a property to ignore files:

    svn propset svn:ignore ‘*.pyc’ ./src/

This example adds an “ignore” rule to the src directory that stops files ending in .pyc from showing as new in svn status. This is very useful for generated content, cache files, or other items that don’t belong under source control. It’s also possible to supply multiple patterns, and to do so by supplying a file that holds these.
The general format of the command is:

    svn propset [property name] [value] [target]

to see which properties are set on a particular file or directory:

    svn proplist index.php

To check the current values of any properties that are set, the svn propget command can be used:

    svn propget svn:ignore src

If a property is set but should be removed, the svn propdel command will do so:

    svn propdel svn:ignore greeting.php

**Admin Features**

to instantiate a new, empty Subversion repository 

    svnadmin create /path/to/new/repo

To safely duplicate a repository to a different location on the file system

    svnadmin hotcopy /path/to/repo /path/to/copy/to

To export your repository

    svnadmin dump /path/to/repo > repo.svndump

To import your repository

    svnadmin load /path/to/repo < repo.svndump

To verify any revision is corrupt or not:

    svnadmin verify

**Shortcuts**

![shortcuts svn](http://i.imgur.com/osZtfhA.png)

**Cheatsheets**

http://www.cheat-sheets.org/saved-copy/subversion-cheat-sheet-v1.pdf

**More**

Online book : http://svnbook.red-bean.com/

SVN code repository hosting service provider:

1. Sourceforge: https://sourceforge.net/

2. Assembla : https://www.assembla.com/subversion/

3. Many more ...


