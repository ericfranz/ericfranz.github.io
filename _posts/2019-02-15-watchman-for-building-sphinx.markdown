---
layout: post
title: "Watchman for Building Sphinx"
date: 2019-02-15T20:25:45-05:00
---

[Watchman](https://facebook.github.io/watchman/) from Facebook is a great little
cross-platform file watching service.

When editing documentation in the Sphinx project https://github.com/OSC/ood-documentation
I open a separate terminal, and start watchman to watch the source directory.
Whenever I save a change watchman rebuilds the application.

    watchman-make -p 'source/**/*.rst' --run 'rm -rf build; rake docker:build'

The key is `-p` tells what files to watch (rst files recursively) and then
`--run` is the command to run when a change occurs.
