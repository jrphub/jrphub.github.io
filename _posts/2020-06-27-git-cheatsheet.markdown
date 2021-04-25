---
title:  "31 - Git Cheatsheet"
date:   2020-06-27 22:36
categories: [Development]
tags: [cheatsheet]
reading_time: 2 min
---

**1. Cherry-pick**

This helps in merging a specific commit from one branch to current branch

```
git cherry-pick <commit-hash>
```

Where *commit-hash* is taken from ```git log```

**2. Changing last commit**‌

To change last commit message

```bash
git commit --amend
```

This will open in vi editor, you can change the commit message and then ```git push```

**3. Changing old commit messages**

```bash
git rebase -i HEAD~N
```

where N is the last N no. of commits

you can see all commits using ```git log``` (console based) or ```gitk``` (UI)‌

**4. To sync local repository with remote repository**

When you clone a repository with *git clone ,* it automatically creates a remote connection called **origin** pointing back to the cloned repository. You may be confused between "origin", "master", "origin/master"‌

```bash
origin : Central or remote repository name
```

```bash
master : a local branch
```

```bash
origin/master : A remote branch (which is a local copy of the branch named "master" on the remote named "origin"
```

Now let's say, you are in **develop** branch, which is ahead of **remote develop** branch. You may push the code to remote branch, but in case you are not interested to push the local changes and make your local branch in sync with remote develop i.e **origin/develop** branch. 

To do this

```bash
git fetch --prune origin
git reset --hard origin/develop
git clean -f -d
git status
```

```git fetch --prune ```or ```git remote prune``` do the same thing: deletes the refs to branches that don't exist on the remote.

`git fetch --prune origin ` will connect to the remote origin and fetch the latest remote state before pruning.

**5. Rename a local and remote branch**

Switch to the local branch which you want to rename:

```bash
git checkout <old-name>
```

Rename the local branch

```bash
git branch -m <new_name>
```

If you have already pushed the *old-name,* branch to the remote repository, run below command to rename the remote branch

Push the *<new-name>,* local branch and reset the upstream branch

```bash
git push origin -u <new-name>
```

Delete the *<old-name>,* remote branch

```bash
git push origin --delete <old-name>
```

**6. Error: cannot lock ref 'refs/remotes/origin/release/.....' : 'refs/remotes/origin/release/' exists**

Here, we need to update the git reference of the release branch like below

```bash
git update-ref -d refs/remotes/origin/release

git pull
```

Thank you :)