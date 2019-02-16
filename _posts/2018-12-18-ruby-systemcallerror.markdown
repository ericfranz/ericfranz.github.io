---
layout: post
title: "Ruby SystemCallError"
date: 2018-12-18T08:26:36-05:00
---

Its a good idea to be aware of all of the subclasses of `SystemCallError`[^1] which are defined in the `Errno`[^2] module. It is easier to use system calls do we rescue from `StandardError`, but sometimes if we want to catch and respond to very specific error conditions (like the file system is read-only `Errno::EROFS` or the disk is full on the device you are trying to write to `Errno::ENOSPC`). Also, rescuing from `SystemCallError` is more specific than `StandardError`.

[This blog post on honeybadger.io](https://blog.honeybadger.io/understanding-rubys-strange-errno-exceptions/) is useful.



[^1]: <https://ruby-doc.org/core-2.4.0/SystemCallError.html>
[^2]: <https://ruby-doc.org/core-2.4.0/Errno.html>
