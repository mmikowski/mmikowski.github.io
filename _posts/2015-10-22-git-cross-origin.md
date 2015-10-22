---
layout: post
title: Using git with multiple remote repositories
---
*When one just isn't good enough*

### Purpose

Recently I was honored to be added to the (taffydb)[http://www.taffydb.org]
maintainer list.  This means I now need to merge changes from multiple
remote repositories.  Yes, this can be done using the GitHub visual interface,
however, often times this simply is not flexible enough.  Luckily, it's rather
easy to set up your git configuration to access all three sources at once.

### Getting set up

Below I illustrate checking out taffydb on the master branch, and then set up
two alternate branches using two different source repositories.

First, let's get the canonical taffydb repository:

    git clone git@github.com:typicaljoe/taffydb.git taffydb

Now let's enter that directory and have a look around:

    cd taffydb
    git remote -v
      # origin  git@github.com:typicaljoe/taffydb.git (fetch)
      # origin  git@github.com:typicaljoe/taffydb.git (push)

Let's add our upstream branch to ltullman.

    # create new branch
    git checkout -b ltullman-master 

    # add remote repository
    git remote add origin-ltullman git@github.com:ltullman/taffydb.git

    # fetch the master branch contents
    git fetch origin-ltullman master

    # and now set upstream
    git branch --set-upstream-to=origin-ltullman/master

    # confirm upstream branch
    git rev-parse --abbrev-ref --symbolic-full-name @{u}
      # origin-ltullman/master

    # confirm origin
    git remote -v
      # origin            git@github.com:typicaljoe/taffydb.git (fetch)
      # origin            git@github.com:typicaljoe/taffydb.git (push)
      # origin-ltullman   git@github.com/ltullman/taffydb.git (fetch)
      # origin-ltullman   git@github.com/ltullman/taffydb.git (push)


At this point, we have an ltullman-master that uses the origin-ltullman
upstream repository. Let's add another:
    
    git checkout -b mmikowski-master 
    git remote add origin-mmikowski git@github:mmikowski/taffydb.git 
    git fetch origin-mmikowski master
    git branch --set-upstream-to=origin-mmikowski/master

And confirm again:

    git rev-parse --abbrev-ref --symbolic-full-name @{u}
      # origin-mmikowski/master

    git remote -v
      # origin            git@github.com:typicaljoe/taffydb.git (fetch)
      # origin            git@github.com:typicaljoe/taffydb.git (push)
      # origin-ltullman   git@github.com/ltullman/taffydb.git (fetch)
      # origin-ltullman   git@github.com/ltullman/taffydb.git (push)
      # origin-mmikowski  git@github.com/mmikowski/taffydb.git (fetch)
      # origin-mmikowski  git@github.com/mmikowski/taffydb.git (push)

Now we have all the sources we want.  Now let's explore what we can do!

### What can we do with this?

I'm still working my way through how to use this and will update this
post as I progress.

Cheers! Mike

