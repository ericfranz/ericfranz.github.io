---
layout: post
title: "Fixing Commit to Master Before Git Pull"
date: 2018-10-24T10:51:03-04:00
---

Sometimes I make a commit to to the master branch and go to push and realize I forgot to do a git fetch. When I do a git fetch I now have a merge problem, that by automerging results in an ugly git history.

I usually avoid this problem by branching for changes. But sometimes you feel pressure to make a quick change. And sometimes when you feel pressure, you want your working directory to remain untouched.


The simple solution:

```ruby
# make a branch
git checkout -b

# update master ref to point to previous commit (like it was before)
git update-ref refs/heads/master e45acbb

# now its safe to git fetch
git fetch
```

Now its safe to git fetch, and I can merge my branch into master when I'm ready.

TODO

- [ ] test and verify we can do `git update-ref refs/heads/master master~1` and update this post
- [ ] test and verify the merge from our branch to master, after the fast forward merge for the master branch and update this post
