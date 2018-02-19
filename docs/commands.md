---
title: Git
permalink: /commands.html
---
# Git commands

## Local commands

### Init

### Add

### Commit

### .gitignore

### Branch
There is nothing particularly special about branches in git - just like HEAD, they are just a 
commit hash stored in a text file in the .git folder.

### Checkout
This will hide any changes you've made to your checkout, make all the files in your checkout the 
same as the tree associated with whatever commit you specified, then re-apply your changes 
(if there are no conflicts).

### Merge
This creates a special kind of commit (a merge commit) which has two parents instead of one. 
What about fast forward? 

### Reset
This command can be used to alter your current checkout - it has two modes of operation:

* Soft
    * Change the current checkout to a different commit, but keep all changes as unstaged edits 
    to files.
* Hard
    * Same as soft but all changes are removed, so your checkout will be exactly the same as the 
    commit you're resetting to.
    * Excluded untracked files/folders by default

### Stash

### Rebase

## Remote commands

### Clone

### Fetch

### Pull

