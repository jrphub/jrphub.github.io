---
title:  "08 - Learning Git"
date:   2017-01-26 11:30:23
categories: [Development]
tags: [git]
reading_time: 3 min
---
Git is a Distributed Version Controlling System (DVCS). it believes work-local, commit-often, publish-when-you-can methodology. It is primarily written in C.

To check the version :

    git --version

To install Git, follow this [url](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git). it's easy.
Configure your identity:

    git config --global user.name “xyz”
    git config --global user.email “xyz@gmail.com”

These commands store your preferences in a file named .gitconfig inside your home directory (~ on UNIX and Mac, and %USERPROFILE% on Windows).

**Github** - [github.com](github.com) makes storing Git repositories and sharing code simple and easy.

**To create a repository or repo :**

 1. github

    - Go to github.com and signup/signin
     - Click on "New Repository"
     - Give your repo name. No need to check "Initialize this repository with a README". Click on "Create repository"

 2. Locally
     From a command prompt, change directories to an empty folder or an existing project directory that you want to put under version control. Then, initialize the directory as a Git repository by typing the following
       commands:
      
      git init
      
      git commit -m "first commit"
      
      git remote add origin https://github.com/username/reponame.git
      
      git push -u origin master
    
If you want to fetch a Github repository to your local :

 - Go to repository page and click on "Clone or Download"
 - Copy the  repo url
 - In your local, run this command:

       git clone <repo-url>

You are ready to make changes now.

**Overal Flow**

![Git flow](https://i.imgur.com/WzIOB6b.png "Git flow")

So you can see, there are **FOUR** areas - workspace (work area), index (stage area), local repository (committed area), remote repository (github repo)

**Adding or Staging**

When new or modified files are ready in your workspace for the next commit, they must first be **staged** with **add** command to move the files from workspace to stage or index area.

    git add index.html =======> by filename
    git add javascript/ ======> by folder name
    git add *.js =============> wildcard pattern

*More operations:*

Moving

    git mv abc.txt xyzDir

Removing

    git rm removeFile.txt

Aborting
If you want to abort all current, uncommitted changes and restore all files to the last committed state

    git reset --hard  ========>  recursively discards all currently uncommitted (unstaged or uncommitted staged) changes

To abort not all, but a few items

    git checkout modifiedfile.txt

To know the status of your local repository

    git status  =======> Very useful command

**Committing**

A commit command transactionally saves the pending additions to the **local repository**.

    git commit -m "message for this commit"
To revert all committed changes

    git checkout HEAD

**Diff**

 A diff compares the patches between any two points in history or changes.

    git diff  =============> compares (uncommitted or last committed) and unstaged files
    git diff --staged =====> compares last committed and staged files
    git diff HEAD =========> compares last committed and modified files in workspace

**Log**

A full list of changes since the initialization of the repository, even when disconnected from all networks

    git log 
    git log -3 ==================> Last 3 commits
    git log --since=yesterday ===> all commits since yesterday

**Blame**

When one is trying to discover why and when a certain line was added

    git blame <filename>

**Branching**

 Git branch is very handy when developer wants to do a quick experiment that may be discarded or merged onto a well known branch depending on their success.

    git branch <new branch name> <from branch>
    git branch <new branch name>
Choosing a branch:

    git checkout <branchname>
Alternatively, the above two steps can be performed in one command i.e.

    git checkout -b <new branch name> <from branch>

To list all local and remote branches:

    git branch -a

 Remote branches are prefixed by “remotes” and are shown in red.

 To delete a branch

     git checkout -d <branch-name>

**Merging**

 Git allows you to merge one or more branches into the current branch.

    git merge <branch one>
    git merge <branch one> <branch two>

If any **conflicts** are encountered, with Git, a notification message is displayed and the files are internally marked with >>>>>>>>> and <<<<<<<< around the conflicting portion of the file contents. Once these conflicts are manually resolved, issue a git add filename command for the resolved file, then commit in the usual manner.

**Rebase**
Rebasing is the 
*rewinding of existing commits* 
*on a branch* 
with the *intent of moving the branch start point forward*, 
then *replaying the rewound commits*. 

This allows developers to test their branch changes safely in isolation on their private branch just as if they were made on top of the mainline code, including any recent mainline bug fixes.

    git rebase <source branch name>
    git rebase <source branch name> <destination branch name>

![enter image description here](https://i.imgur.com/bZZzfN6.png "git-rebase")

**Confused between "git rebase" and "git merge"?**

Both of these commands are designed to integrate changes from one branch into another branch—they just do it in very different ways. **How?**

Situation is like this, you are working on "feature" branch and in the mean time, some more commits happened in "master" branch and you need those changes for your work. Shown as below:

![git commit history](https://i.imgur.com/eJxOlEq.png "git-situation")

You have two options:

    1. merge
    2. rebase

*Merge option*

Merge master branch into the feature branch using below commands

    git checkout feature
    git merge master
or,

    git merge master feature

This creates a new "merge commit" in the feature branch, shown below
![git merge](https://i.imgur.com/wnvGhbK.png "git merge")

Merging is nice because it’s a non-destructive operation. The existing branches are not changed in any way. This avoids all of the potential pitfalls of rebasing (discussed below).
Only problem is , If master is very active, this can pollute your feature branch’s history quite a bit.

*Rebase option*
You can rebase the feature branch onto master branch using the following commands:

    git checkout feature
    git rebase master

This moves the entire feature branch to begin on the tip of the master branch, effectively incorporating all of the new commits in master. But, instead of using a merge commit, rebasing re-writes the project history by creating brand new commits for each commit in the original branch, as shown below:

![Merging-vs-Rebasing](https://i.imgur.com/Yz1ucBM.png "Merging-vs-Rebasing")

Though you get cleaner project history, you can’t see when upstream changes were incorporated into the feature. So, before you run git rebase, always ask yourself, “Is anyone else looking at this branch?” If the answer is yes, take your hands off the keyboard and start thinking about a non-destructive way to make your changes (e.g., the git revert command). Otherwise, you’re safe to re-write history as much as you like.

**Commit hashes  & Shorthand**

Git marks each commit with a SHA–1 hash that is unique to the user committing the changes. A full SHA–1 hash is 40 hex characters, such as 64de179becc3ed324daab72f7238df1404723672.
To efficiently navigate the history of hashes, several symbolic shorthand notations can be used as listed in the table below:

![git shothand](https://i.imgur.com/u5q76EC.png)

    git diff HEAD~3

**Advance, but Useful Commands**

**Stash** (Save your work before doing another work)

You are in the mid of your work and your changes are not ready for commit yet. You want to move to last commit to fix a bug. The solution is :

*Keep Calm and use git stash*

Using stash on modified files places all uncommitted changes onto a stack

    git stash

When you are ready to write the stashed changes back into the working copy of the files, simply pop them back off the stack.

    git stash pop

Stashing will only set aside modified, tracked files. New, untracked files will remain in untouched and in the working directory.

**Tagging**
Git provides tagging to mark a specific commit in your timeline of changes, and serve as a useful identifier in history. 

    git tag <tag name>

To list all tags

    git tag

Best Practice is:

*Branch out, Merge often and keep always in sync*

**Working with remote repository**
Unlike SVN's central repository model, Git follows "Collaboration model", which gives every developer their own copy of the repository.
For example, the following diagram shows two remote connections from your repo into the central repo and another developer’s repo. Instead of referencing them by their full URLs, you can pass the origin and john shortcuts to other Git commands.
![git-remote](https://i.imgur.com/1GlNz8z.png "git-remote")

To get full address of your configured remote:

    git remote -v

To add new remote :

    git remote add <name> <url>

To remove the connection to the remote repository

    git remote rm <name>

To rename a remote connection from old name to new name

    git remote rename <old-name> <new-name>

**Secrets of "origin"**

When you clone a repository with **git clone**, it automatically creates a remote connection called **origin pointing back to the cloned repository.** This is useful for developers creating a local copy of a central repository, since it provides an easy way to pull upstream changes or publish local commits. This behavior is also why most **Git-based projects call their central repository origin**.

*Confused between "origin", "master", "origin/master"? hmm..*

- *origin : Central or remote **repository** name*
- master: A local **branch**
- *origin/master : A remote **branch** (which is a local copy of the branch named "master" on the remote named "origin")*

**Push**

The push command performs a publishing action, and sends the Git commit history of one or more branches to an upstream Git repository.

    git push <remote name> <branch name>

**Fetch**

It imports commits from a remote repository into your local repo. The resulting commits are stored as **remote branches instead of the normal local branches** that we’ve been working with. This gives you a chance to review changes before integrating them into your copy of the project.

    git fetch <remote>
    git fetch <remote> <branch> ==========> Fetch a specified branch
Then you can use

    git checkout master
    git merge orgin/master

The origin/master and master branches now point to the same commit, and you are synchronized with the upstream developments.

**Pull** (fetch + merge)

Fetch the specified remote’s copy of the current branch and immediately merge it into the local copy

    git pull <remote>

**How to fork a project or contribute to Open source project?**

To contribute to other's project, we need follow two steps
1. Fork the repository (go to git hub repository and click on "fork"
2. After the changes done, make a Pull request

This is best described [here](https://www.atlassian.com/git/tutorials/making-a-pull-request/example) and a **MUST** read (Don't miss it)

For **GUIS**

    git gui

or

    gitk --all

**When Git flirts with SVN**

To retrieve a local working copy of a Subversion repository that uses the traditional structure (trunk, tags, branches) and convert it into a Git repository, 

    git svn clone --stdlayout <svn-repo-url>

To commit to SVN repository

    git svn dcommit

To make Git repository up-to-date

    git svn rebase

**When SVN flirts with Git**

To perform a traditional SVN checkout against a repository on GitHub 

    svn checkout https://github.com/<username>/<project>

**More sources:**

1. http://rogerdudler.github.io/git-guide/
2. https://www.atlassian.com/git/tutorials/what-is-version-control
