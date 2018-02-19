---
title: Git
permalink: /git.html
---
# Git baby

## Introduction
git is a Distributed Version Control System (DVCS).
It is used for tracking changes to files and folders.
All the git data is usually kept in a single folder named .git in the same folder that is being 
version controlled. This .git folder is referred to as a git repository.

### What is git?
At its core git is a simple key-value object store. Everything else is just scripts on top to 
co-ordinate the store. It's important to know this as it helps you better visualise what git is 
actually doing, and gives you a better understanding of why the commands work the way they do.

I will cover the basics of the internals of git, going into as little detail as possible to give a 
general understanding; this may mean I fudge some details that are only important if you're 
interested in developing on git itself -- this guide is aimed at beginner/intermediate git users.

## Prerequisites
To follow this document you'll need to be familiar with the command line and have git installed.
To install git see: [https://git-scm.com/book/en/v2/Getting-Started-Installing-Git](Installing git)


>"I decided I could write something better than everything out there in two weeks. And I was right."
>--Linus Torvalds

[https://www.youtube.com/watch?v=4XpnKHJAok8](Linus Torvalds on git)


## Git by example
To explain the basics of git we'll start by creating an empty git repository:

```
mkdir git-tutorial
cd git-tutorial
git init
```

This will create a .git folder inside the directory. The command only sets up the folder structure
for git, it doesn't add any real content.

There are four object file types in git:
1. Blob - a file
2. Tree - a folder
3. Commit - a tree with metadata including author, message and timestamp
4. Tag - a label for a commit

### Baby's first file
Create a new file in the git_tutorial folder:
```
echo "hello git" > test.txt
```

To see the state of our git repository we can use the `git status` command:
```
$ git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        test.txt

        nothing added to commit but untracked files present (use "git add" to track)
```

Stage the file in git
```
git add test.txt
```

**What is staging?**

Unlike most version control systems, git has a two step process to put a file into a git repository. 
You first tell git all the files you want to add, then a separate command is run to commit the 
files into the repository. If you're familiar with database transactions, it is a similar concept.

The process of adding files before committing them is called staging:

![git staging]({{ site.baseurl }}/public/images/git/staging.png)

The main benefits of this two step process are:
* You can double check everything before committing (git diff --staged will show you all 
differences you're about to commit)
* You can selectively add changes -- git has an interactive add which will let you add just part 
of your file

We now see this file as a change to be committed, rather than an untracked file:
```
$ git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

          new file: test.txt
```

If we commit the file we will complete the process:

```
git commit -m "Added a file"
```

The message "Added a file" is a description that we add to the commit to 
add context to changes we've made in git.

This has created three files in our previously empty repository:

```
.git/objects
├── 20
│   └── bf064f310e3386fb9681849c184a4ec16125b0 # tree
├── 8d
│   └── 0e41234f24b6da002d962a26c2495ea16a425f # blob
├── 92
│   └── 085a0f8205044818b1a791801b67e24a535d6f # commit
├── info
└── pack
```

### Subfolders
We will now create a subfolder and add a new file inside it:

```
mkdir test_folder
echo "hello subfolder" > test_folder/test2.txt
git add test_folder/test2.txt
git commit -m "Added second test file"
```

### Tags
create a tag:

```
git tag -m "big tag" test_tag
```

### Verify

You can check the contents of all the files in git very easily, for example in python:

```python
In [1]: import os, zlib

In [2]: def get_object(key):
...:      key_path = os.path.join(".git", "objects", key[:2], key[2:])
...:      with open(key_path, 'rb') as fh:
...:          return zlib.decompress(fh.read())
...:
```

The contents of the files listed above:
```python
In [3]: get_object('8d0e41234f24b6da002d962a26c2495ea16a425f') # the test.txt blob file
Out[3]: b'blob 10\x00hello git\n'

In [4]: get_object('20bf064f310e3386fb9681849c184a4ec16125b0') # the tree
Out[4]: b'tree 36\x00100644 test.txt\x00\x8d\x0eA#O$\xb6\xda\x00-\x96*&\xc2I^\xa1jB_'

In [5]: get_object('92085a0f8205044818b1a791801b67e24a535d6f') # the commit
Out[5]: b'commit 181\x00tree 20bf064f310e3386fb9681849c184a4ec16125b0\nauthor Alex Hodgkins <alex@localhost> 1516535415 +0000\ncommitter Alex Hodgkins <alex@localhost> 1516535415 +0000\n\nAdded a file\n'

In [6]: get_object('c6208e4af8bc03b81a0c847fddab72af35119875') # the tag
Out[6]: b'tag 141\x00object d8827243e3736a25f7a28f5711f399a1985362f1\ntype commit\ntag test_tag\ntagger Alex Hodgkins <alex@localhost> 1517154254 +0000\n\nbig tag\n'
```

Git also comes with tools for viewing the contents of internal objects (e.g. git cat-file). 
See [https://git-scm.com/book/en/v2/Git-Internals-Git-Objects](Git Internals) for full details

You can also check the SHA-1 generation of a blob yourself with python:

```python
In [1]: import hashlib

In [2]: def git_sha(text):
...:      header = "blob {}\0".format(len(text))
...:      hash = hashlib.sha1()
...:      hash.update(header + text)
...:      return hash.hexdigest()
...:
```

Remember to include the newline or you will get very different keys:

```
In [3]: git_sha("hello git\n")
Out[3]: '8d0e41234f24b6da002d962a26c2495ea16a425f'

In [4]: git_sha("hello git")
Out[4]: 'f09e9c379f5fe8f4ce718641c356df87906d87a6'
```

### HEAD
Not actually an object in git but it's very important to know about because you'll see HEAD come 
up a lot in git examples. HEAD is a text file in your git folder that typically refers to another 
branch.
It is a plaintext file so you can easily look at the contents:

```
$ cat .git/HEAD
ref: refs/heads/master
```

This is what tells git what branch you're currently on -- whenever you change branch HEAD is 
updated.

It doesn't have to point to another branch -- it can also point to a single commit. 
You can do this by checking out a commit instead of a branch, which will give you the warning:

```
$ git checkout cf7d8eb2f3a3bc44e8b8c89b01334459b943edc9
Note: checking out 'cf7d8eb2f3a3bc44e8b8c89b01334459b943edc9'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

      git checkout -b <new-branch-name>

      HEAD is now at cf7d8eb... Initial commit
```

This means you are not on a branch, so if you add more commits they will be floating in the tree 
with no associated branch. Remember that a branch in git is really just a label for a specific 
commit.
