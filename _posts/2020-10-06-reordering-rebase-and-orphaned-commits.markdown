---
layout: post
title: "Reordering git commits in a rebase"
date: 2020-10-06T10:51:03-04:00
---

When you want a clean PR and you have two changes in a PR and one modification, such as add a comment, remove newlines, or some other minor modification that should be included in the first change, you can reorder commits in a rebase.

Say my git history of a repo is:

```
* c26d21b - (HEAD -> master) add two comment (1 second ago) <Eric Franz>
* 6ccad4d - add back one comment (1 second ago) <Eric Franz>
* 658b016 - add two (7 seconds ago) <Eric Franz>
* 676dd72 - add one (3 weeks ago) <Eric Franz>
```

The two legitimate changes are the first two: 676dd72 and 658b016. The other two commits are adding comments. I want a clean PR and my history to look like:

```
* 658b016 - add two (7 seconds ago) <Eric Franz>
* 676dd72 - add one (3 weeks ago) <Eric Franz>
```

I can reorder the commits in a rebase to do this:

```
$ git rebase -i --root
pick 676dd72 add one
pick 658b016 two
pick 6ccad4d add back one comment
pick c26d21b add two comment

# Rebase c26d21b onto c26d21b (4 commands)
```

by just changing the order of the rows and specifying the comment commits to squash:

```
pick 676dd72 add one
squash 6ccad4d add back one comment
pick 658b016 two
squash c26d21b add two comment
```

Now my history is as I desired:

```
* 78fadaf - (HEAD -> master) two (5 seconds ago) <Eric Franz>
* 02c0e91 - add one (11 seconds ago) <Eric Franz>
```
