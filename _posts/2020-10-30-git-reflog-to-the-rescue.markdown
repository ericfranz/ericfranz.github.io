---
layout: post
title: "Git Reflog to the Rescue"
date: 2020-10-30T10:46:52-04:00
---

Some smaller apps that we needed to quickly setup and periodically update we don't fully manage with Puppet yet. Instead the code is deployed as a git clone of a repo to a service account's home directory and Puppet is used to manage the application processes. To update the app, you do a git pull and then restart.

Once I did a git pull, app restart, and curl to start the app and then realized I needed to revert. But I'm not the developer of this app - and the developers are not tagging the previous versions they deploy, so how do I know which commit to revert to? The reflog!

    $ git reflog
    [covid@web08 cats]$ git reflog --date=iso
    d9bbac0 HEAD@{2020-10-30 10:39:25 -0400}: pull: Fast-forward
    3020062 HEAD@{2020-10-27 09:12:42 -0400}: pull: Fast-forward
    c7889d3 HEAD@{2020-10-23 12:51:03 -0400}: pull: Fast-forward
    7c2727e HEAD@{2020-10-20 17:24:07 -0400}: pull: Fast-forward

The latest commit d9bbac0 is what I just deployed this morning. The last deployment was 3020062 on October 27, two days ago. That looks safe to revert to.

    $ git checkout 3020062

Now I restart the app and I'm good to go! Of course I have to remember I'm in a detached state and need to checkout the main branch before doing another git pull.

