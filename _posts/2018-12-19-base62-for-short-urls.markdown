---
layout: post
title: "Base62 for Short Urls"
date: 2018-12-19T10:21:50-05:00
---

Discourse [uses base62 for short urls for file uploads](https://meta.discourse.org/t/quick-image-resizing-and-markdown-image-dimensions/66812/30) and the reason is made clear by [this comment](https://meta.discourse.org/t/quick-image-resizing-and-markdown-image-dimensions/66812/30):

>(note that we’ll need to use base62 3, to avoid the / and + chars in URLs though)
>![Base64's last two chars are / and +](/assets/base64.png)

And this is the Ruby library they use: https://github.com/steventen/base62-rb