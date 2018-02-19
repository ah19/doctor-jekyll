---
title: Git
permalink: /working-with-git.html
---
# Working with git
Git has two important use cases - developer and reviewer/merger. Developer doesn't care about 
commits/prs. History should tell a story.

## Tips

### Pull requests are essential
All code being committed should be looked at by at least one other person. Even if it's just two 
of you on a project, why not? If the rules below are followed reviewing does not take much time, 
and everything gets a sanity check. For senior devs you are really just skimming the code, under 
the assumption it's mostly correct. For junior devs you can easily give them instant feedback on 
all code they write.

### Branches must be small
A branch should be as small as you can sensibly make it. This will vary depending on the feature. 
It will also seem impossible at first, but there is no harm adding code to your repo that is not 
called anywhere yet; it should have tests anyway, so you know it works. It's also part of a 
feature you're intending to complete in the near future, so it's not the same as leaving in some 
code that will never be used. If you decide the feature will not be implemented and the code is now 
redundant it can easily be removed - you can simply revert the commits/pull requests and it's gone.

If it is a change to existing behaviour you can use feature flags to turn the new code off in 
production. This has the added benefit of having you

Migrations can be tricky, but if it is only adding tables/columns these can be done immediately. 
For breaking changes, consider modifying the code to work with both. This has the added benefit of 
meaning you can do partial migrations, with all new entries using the new code, giving you more 
time to migrate the existing ones without needing downtime.

I say consider because none of these are hard rules; there's always exceptions. I am simply asking 
you to consider all options and their drawbacks before saying you need a large feature branch. 
I've seen these stay open for years and divert so far from the core codebase they can't be 
merged back - it encourages laziness, letting you add things without having to fully consider 
their consequences until merge time. If you know it must be merged next week you will put that 
effort in up front and save yourself (and others) a massive headache.

I would recommend not having branches open for longer than 2 weeks (or a single agile sprint).

This may seem like a lot of extra work, but in the long term it is far less; it is much harder to 
make poor choices if the rules are enforced.

It also means if someone implements something in completely the wrong way you only have to throw 
away a small amount of work. A pull request with 3 months of work in is impossible to review - 
reviewing is boring; you will miss things, you will find things that are too big to change. 
You can't throw it away because someone spent 3 months on it. Maybe you should have talked about 
the design more beforehand, maybe you should have checked more closely as it was being developed; 
with a small pull request it doesn't matter as not much is lost.

### Commits must group related functionality
Unrelated code changes must not be in the same commit. As your project gets larger you may find 
yourself with multiple releases that need the same code change - if your commits are small and 
atomic you can easily cherry pick them between branches with few merge conflicts. If your commit 
changes 2 unrelated things you'll now have to tease out the changes you do want, which takes far 
more time than committing sensibly in the first place. It also makes git blame much more readable 
as your short commit messages will be much shorter and easier to describe.

### All branches must be rebased before merging
There seems to be a lot of confusion about merge stategies around the Internet. 
People think rebasing is the devil.
You never want to rebase your public dev and master branches - they should be considered your 
actual immutable history of events.

### All branches branches must be merged with -no-ff
This does not include branches off of feature branches - only those going into dev/master. You can 
do it for release branches if you like but there shouldn't be too many commits in there so it 
    matters less.

**Diagram of difference**


## Workflow

### Recommended Workflow
Git is the gatekeeper to your codebase.

This is subjective; there are a lot of different suggested workflows, which largely come down to 
tradeoffs between history readability and effort. On a small project with few developers it matters 
much less, but the larger the project the messier it can get. It also partly depends on your release 
process. The cleanest and most flexible I have found is a slightly modified version of git flow.

