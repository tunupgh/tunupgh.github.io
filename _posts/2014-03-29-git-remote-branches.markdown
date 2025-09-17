---
layout: post
title: Git Remote Branches
date: 2014-03-29 11:05
comments: true
categories:
- git
- coding
- scripts
---
Git is a powerful document repository and version control system. This page will show you how to handle git remote branches. This page assumes you know the basics of git and you're ready for a slightly more advanced topic. An excellent page to get started with git is [git - the simple guide](http://rogerdudler.github.io/git-guide/).


##What is a branch?
The best case for using a git branch is when making a new feature on a project you're working on. You may not want to disturb the master branch until your new feature is complete. This way you can still push bug fixes or smaller features to the master branch without worrying about your new developmental code getting pushed. Then when you're new feature is ready to be merged into the master branch you can merge it all together and delete that branch.

##Creating a New Local Branch
To create a local branch. Simply do this:

``` 
git checkout -b newfeature
```

The -b creates the branch and then moves you to that branch.

Now take a look at your branches:
```
$ git branch
  master
* newfeature
```
The '*' means that's the branch you're currently working on. When you're on that branch you can make changes, do `git add`, `git commit` etc.

To switch back to the master branch do this:
```
git checkout master
```
It is important to make sure that if you've done changes on the branch to add and commit them before switching to another branch. Otherwise your changes may be added to the wrong branch.

##Push the local branch to the repository
Pushing local branches to the repo is almost the same as pushing the master branch to the repo. Simply change master to the branch name. The command would look like this:

```
git push origin newfeature
```
 
##Getting a Remote Branch and Making Changes to it
Now lets say someone else wants to get that branch and make changes to it also. They can begin tracking it by doing this from the master branch:

```
git pull
```

Sometimes, for me at least, `git pull` gives me an error like 'fatal: No remote repository specified.' If this happens to you first do `git pull -u origin master` then do `git pull`.

 
Once they do that, they should now be tracking that remote branch. Use this command to confirm.
```
$ git branch -a
* master
  remotes/origin/master
  remotes/origin/newfeature
```

At this point they aren't ready to use or modify that branch. It's still a remote branch. In order to do work on the remote branch they need to create a pointer between a new local branch and that remote branch. To do that they would do this:
```
git checkout -b newfeature origin/newfeature
```

What that command does is create the local "newfeature" branch, and moves them to that branch. But it uses the remote origin/newfeature branch as the reference for the local branch they just made. The reference is important because if they do ‘git push’ later it will know to push any reference branches too.

Now they should be on their local newfeature branch (`git branch -a` to confirm). From here they can do changes and commit. When ready and they want to push it back to the repo they can do this from the newfeature branch:

```
git add .
git commit -m ‘changes’
git push origin newfeature
```


##Merging Remote Branch to Local Branch

Now let’s say you made more changes to the newfeature branch and your collaborator wants to pull those changes into their local branch. Or another example would be your collaborator has made changes and pushed them to the repo and you want to pull them into your local branch.

First switch to the master branch then pull.
```
git checkout master  
git pull
```
Sometimes, for me at least, `git pull` will ask me where do I want to fetch from today. In that case I first do `get pull -u origin master` then `git pull`.

You should see something about the newfeature branch being unpacked which means you've tracked the changes done to that remote branch.

You aren’t done yet. All that’s happened is that your repo is now aware the remote branch is updated. So switch over to the branch and merge it with your local branch.

```
git checkout newfeature
git merge origin/newfeature
```
 
This command will now merge the remote branch to your local branch.
 

##Merging to Master

When we are done with the branch and want to merge it into the master we simply do this:
```
git checkout master
git merge newfeature 
```
This command will merge what you specify into the branch you’re in

 
##Deleting Branches

Then if we are done with the newfeature branch we should delete it.

Delete the local newfeature branch by doing

```
git branch -d newfeature
```

Delete the remote newfeature branch by doing
```
git push origin :newfeature
```
The ':'' indicates the remote branch should be deleted.


### Additional Reading
 [Git Branching - Remote Branches](http://www.git-scm.com/book/ch3-5.html)