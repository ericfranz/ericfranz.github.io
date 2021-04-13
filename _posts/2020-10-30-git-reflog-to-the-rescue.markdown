---
layout: post
title: "Git Reflog to the Rescue"
date: 2020-10-30T10:46:52-04:00
---

Yet again the git reflog saves the day. Several times it has happened that I accidentally deployed a new version of an app in production. _Note: this is about an app that was not managed by Puppet, but instead was deployed by manually cloning the app repo._

I did a git pull, app restart, and curl to start the app and then realized I need to revert. But I'm not the developer of this app - and the developers are not tagging the previous versions they deploy, so how do I know which commit to revert to? The reflog!

    $ git reflog
    [covid@web08 cats]$ git reflog --date=iso
    d9bbac0 HEAD@{2020-10-30 10:39:25 -0400}: pull: Fast-forward
    3020062 HEAD@{2020-10-27 09:12:42 -0400}: pull: Fast-forward
    c7889d3 HEAD@{2020-10-23 12:51:03 -0400}: pull: Fast-forward
    7c2727e HEAD@{2020-10-20 17:24:07 -0400}: pull: Fast-forward

The latest commit d9bbac0 is what I just deployed this morning. The last deployment was 3020062 on October 27, two days ago. That looks safe to revert to.

    $ git checkout 3020062

Now I restart the app and I'm good to go! Of course I have to remember I'm in a detached state and need to checkout the main branch before doing another git pull.

