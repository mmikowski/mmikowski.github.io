---
layout: post
title: Using git with multiple remote repositories
---
*When one just isn't good enough*

### Purpose

Recently I was honored to be added to the [taffydb](http://www.taffydb.org)
maintainer list.  This means I now need to merge changes from multiple
remote repositories.  Yes, this can be done using the **GitHub** web
interface.  Sometimes, though, this simply is not flexible enough.  Luckily,
it's rather easy to set up your `git` working directory to access all 
three sources at once and merge between them.

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
    git remote add ltullman git@github.com:ltullman/taffydb.git

    # fetch the master branch contents
    git fetch ltullman master

    # and now set upstream
    git branch --set-upstream-to=ltullman/master

    # confirm upstream branch
    git rev-parse --abbrev-ref --symbolic-full-name @{u}
      # ltullman/master

    # confirm origin
    git remote -v
      # ltullman   git@github.com/ltullman/taffydb.git (fetch)
      # ltullman   git@github.com/ltullman/taffydb.git (push)
      # origin     git@github.com:typicaljoe/taffydb.git (fetch)
      # origin     git@github.com:typicaljoe/taffydb.git (push)


At this point, we have an ltullman-master that uses the ltullman
upstream repository. Let's add another:
   
    git checkout master
    git checkout -b mmikowski-master 
    git remote add mmikowski git@github.com:mmikowski/taffydb.git 
    git fetch mmikowski master
    git branch --set-upstream-to=mmikowski/master

And confirm again:

    git rev-parse --abbrev-ref --symbolic-full-name @{u}
      # mmikowski/master

    git remote -v
      # ltullman   git@github.com/ltullman/taffydb.git (fetch)
      # ltullman   git@github.com/ltullman/taffydb.git (push)
      # mmikowski  git@github.com/mmikowski/taffydb.git (fetch)
      # mmikowski  git@github.com/mmikowski/taffydb.git (push)
      # origin     git@github.com:typicaljoe/taffydb.git (fetch)
      # origin     git@github.com:typicaljoe/taffydb.git (push)

Now we have all the sources we want.  Now let's explore what we can do!

### What can we do with this?

I found that when setting up multiple upstream sources like this, we need to
do a little work to get the branch to be "pristine".  For example, I found a
merge conflict in my local copy of mmikowski-master.  I wanted that fixed,
and to match the upstream copy exactly.  So I cloned another local copy and
then made sure my mmikowski-master branch matched it exactly:

    git checkout master-mmikowski
    cd ..
    git clone git@github.com/mmikowski/taffydb.git taffydb-mmikowski

    # visual diff tool to merge remote master to local - copy all files
    #  from source (left)
    kdiff3 taffydb-mmikowski taffydb

    # resolve conflicts
    cd taffdb
    git add .
    git commit -a

    # And push 
    git push mmikowski HEAD:master

We can do the same check with ltullman.  Now we can cherry-pick 
changes from a branch and merge them into master.  In the example below,
I merged over my regression tests but left other changes alone as I didn't feel
they were well-tested enough.

    checkout master
    git difftool mmikowski-master taffy-test.html


### Why not just use pull-requests?

Atomic pull-requests that can be resolved from the **GitHub** web interface
are always preferred if possible.  However, sometimes circumstances require
more nuanced merging, and this is where having remote repositories for each
branch is very, very handy.

Cheers, Mike


