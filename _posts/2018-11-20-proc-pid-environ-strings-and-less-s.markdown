---
layout: post
title: "/proc/PID/environ, strings, and less -S"
date: 2018-11-20T09:46:03-05:00
---

## TLDR

1. pipe `cat /proc/3268/environ` to `strings` to get friendly output
2. Use `less -S` to drop line wrap in 

## I want newlines. I don't want line wrapping. I'm very needy.

For a running process, given that process's id, you can see the initial environment of the process by cat-ing the environ file in /proc/PID:

    $ sudo cat /proc/3268/environ
    MANPATH=/opt/rh/rh-ruby24/root/usr/local/share/man:/opt/rh/rh-ruby24/root/usr/share/man:/opt/rh/rh-nodejs6/root/usr/share/man:/opt/rh/rh-git29/root/usr/share/man:/opt/rh/rh-passenger40/root/usr/share/man:/opt/rh/nginx16/root/usr/share/man:TERM=unknownSHELL=/bin/bashPERL5LIB=/opt/rh/rh-git29/root/usr/share/perl5/vendor_perlX_SCLS=rh-ruby24 rh-nodejs6 rh-git29 rh-passenger40 nginx16 USER=oodLD_LIBRARY_PATH=/opt/rh/rh-ruby24/root/usr/local/lib64:/opt/rh/rh-ruby24/root/usr/lib64:/opt/rh/rh-nodejs6/root/usr/lib64:/opt/rh/httpd24/root/usr/lib64:/opt/rh/rh-passenger40/root/usr/lib64SUDO_USER=apacheSUDO_UID=48USERNAME=rootPATH=/opt/rh/rh-ruby24/root/usr/local/bin:/opt/rh/rh-ruby24/root/usr/bin:/opt/rh/rh-nodejs6/root/usr/bin:/opt/rh/rh-git29/root/usr/bin:/opt/rh/rh-passenger40/root/usr/bin:/opt/rh/rh-passenger40/root/usr/sbin:/opt/rh/nginx16/root/usr/bin:/opt/rh/nginx16/root/usr/sbin:/sbin:/bin:/usr/sbin:/usr/binMAIL=/var/mail/rootPWD=/LANG=CSHLVL=0SUDO_COMMAND=/opt/ood/nginx_stage/sbin/nginx_stage pun -u ood -a http%3a%2f%2flocalhost%3a8080%2fnginx%2finit%3fredir%3d%24http_x_forwarded_escaped_uriHOME=/rootPYTHONPATH=/opt/rh/rh-nodejs6/root/usr/lib/python2.7/site-packagesLOGNAME=rootXDG_DATA_DIRS=/opt/rh/rh-ruby24/root/usr/local/share:/opt/rh/rh-ruby24/root/usr/share:/usr/local/share:/usr/sharePKG_CONFIG_PATH=/opt/rh/rh-ruby24/root/usr/local/lib64/pkgconfig:/opt/rh/rh-ruby24/root/usr/lib64/pkgconfig:/opt/rh/rh-passenger40/root/usr/lib64/pkgconfig:/opt/rh/nginx16/root/usr/lib64/pkgconfigSUDO_GID=48ONDEMAND_VERSION=1.4.3ONDEMAND_PORTAL=ondemandONDEMAND_TITLE=Open On

Unfortunately, the result is a little unweildy. `strings` can help with that:

    $ sudo cat /proc/3268/environ | strings
    MANPATH=/opt/rh/rh-ruby24/root/usr/local/share/man:/opt/rh/rh-ruby24/root/usr/share/man:/opt/rh/rh-nodejs6/root/usr/share/man:/opt/rh/rh-git29/root/usr/share/man:/opt/rh/rh-passenger40/root/usr/share/man:/opt/rh/nginx16/root/usr/share/man:
    TERM=unknown
    SHELL=/bin/bash
    PERL5LIB=/opt/rh/rh-git29/root/usr/share/perl5/vendor_perl
    X_SCLS=rh-ruby24 rh-nodejs6 rh-git29 rh-passenger40 nginx16
    USER=ood
    LD_LIBRARY_PATH=/opt/rh/rh-ruby24/root/usr/local/lib64:/opt/rh/rh-ruby24/root/usr/lib64:/opt/rh/rh-nodejs6/root/usr/lib64:/opt/rh/httpd24/root/usr/lib64:/opt/rh/rh-passenger40/root/usr/lib64
    SUDO_USER=apache
    SUDO_UID=48
    USERNAME=root
    ...
    

Much better. Now I want to see how the environment for Ruby is setup.

    $ sudo cat /proc/3268/environ | strings | grep ruby
    MANPATH=/opt/rh/rh-ruby24/root/usr/local/share/man:/opt/rh/rh-ruby24/root/usr/share/man:/opt/rh/rh-nodejs6/root/usr/share/man:/opt/rh/rh-git29/root/usr/share/man:/opt/rh/rh-passenger40/root/usr/share/man:/opt/rh/nginx16/root/usr/share/man:
    X_SCLS=rh-ruby24 rh-nodejs6 rh-git29 rh-passenger40 nginx16
    LD_LIBRARY_PATH=/opt/rh/rh-ruby24/root/usr/local/lib64:/opt/rh/rh-ruby24/root/usr/lib64:/opt/rh/rh-nodejs6/root/usr/lib64:/opt/rh/httpd24/root/usr/lib64:/opt/rh/rh-passenger40/root/usr/lib64
    PATH=/opt/rh/rh-ruby24/root/usr/local/bin:/opt/rh/rh-ruby24/root/usr/bin:/opt/rh/rh-nodejs6/root/usr/bin:/opt/rh/rh-git29/root/usr/bin:/opt/rh/rh-passenger40/root/usr/bin:/opt/rh/rh-passenger40/root/usr/sbin:/opt/rh/nginx16/root/usr/bin:/opt/rh/nginx16/root/usr/sbin:/sbin:/bin:/usr/sbin:/usr/bin
    XDG_DATA_DIRS=/opt/rh/rh-ruby24/root/usr/local/share:/opt/rh/rh-ruby24/root/usr/share:/usr/local/share:/usr/share
    PKG_CONFIG_PATH=/opt/rh/rh-ruby24/root/usr/local/lib64/pkgconfig:/opt/rh/rh-ruby24/root/usr/lib64/pkgconfig:/opt/rh/rh-passenger40/root/usr/lib64/pkgconfig:/opt/rh/nginx16/root/usr/lib64/pkgconfig

Great! And I can open this in less which lets me navigate around:

    $ sudo cat /proc/3268/environ | strings | grep ruby | less

```
MANPATH=/opt/rh/rh-ruby24/root/usr/local/share/man:/opt/rh/rh-ruby24/root/usr/share/man:/opt/rh/rh-nodejs6/root/usr/share/man:/opt/rh/rh-git29/root/usr/share/man:/opt/rh/rh-passenger40/root/usr/share/man:/opt/rh/nginx16/root/usr/share/man:
X_SCLS=rh-ruby24 rh-nodejs6 rh-git29 rh-passenger40 nginx16
LD_LIBRARY_PATH=/opt/rh/rh-ruby24/root/usr/local/lib64:/opt/rh/rh-ruby24/root/usr/lib64:/opt/rh/rh-nodejs6/root/usr/lib64:/opt/rh/httpd24/root/usr/lib64:/opt/rh/rh-passenger40/root/usr/lib64
PATH=/opt/rh/rh-ruby24/root/usr/local/bin:/opt/rh/rh-ruby24/root/usr/bin:/opt/rh/rh-nodejs6/root/usr/bin:/opt/rh/rh-git29/root/usr/bin:/opt/rh/rh-passenger40/root/usr/bin:/opt/rh/rh-passenger40/root/usr/sbin:/opt/rh/nginx16/root/usr/bin:/opt/rh/nginx16/root/usr/sbin:/sbin:/bin:/usr/sbin:/usr/bin
XDG_DATA_DIRS=/opt/rh/rh-ruby24/root/usr/local/share:/opt/rh/rh-ruby24/root/usr/share:/usr/local/share:/usr/share
PKG_CONFIG_PATH=/opt/rh/rh-ruby24/root/usr/local/lib64/pkgconfig:/opt/rh/rh-ruby24/root/usr/lib64/pkgconfig:/opt/rh/rh-passenger40/root/usr/lib64/pkgconfig:/opt/rh/nginx16/root/usr/lib64/pkgconfig
(END)
```

But the line wrap makes it a little difficult so lets see it all on one line:

    $ sudo cat /proc/3268/environ | strings | grep ruby | less -S

```
MANPATH=/opt/rh/rh-ruby24/root/usr/local/share/man:/opt/rh/rh-ruby24/root/usr/share/man:/opt/rh/rh-nodejs6/root/usr/share/man:/opt/rh/rh-git29/root/usr/share/man:/opt/rh/rh-passenger40/root/usr/share/man:/opt/rh/nginx16/root/u
X_SCLS=rh-ruby24 rh-nodejs6 rh-git29 rh-passenger40 nginx16
LD_LIBRARY_PATH=/opt/rh/rh-ruby24/root/usr/local/lib64:/opt/rh/rh-ruby24/root/usr/lib64:/opt/rh/rh-nodejs6/root/usr/lib64:/opt/rh/httpd24/root/usr/lib64:/opt/rh/rh-passenger40/root/usr/lib64
PATH=/opt/rh/rh-ruby24/root/usr/local/bin:/opt/rh/rh-ruby24/root/usr/bin:/opt/rh/rh-nodejs6/root/usr/bin:/opt/rh/rh-git29/root/usr/bin:/opt/rh/rh-passenger40/root/usr/bin:/opt/rh/rh-passenger40/root/usr/sbin:/opt/rh/nginx16/ro
XDG_DATA_DIRS=/opt/rh/rh-ruby24/root/usr/local/share:/opt/rh/rh-ruby24/root/usr/share:/usr/local/share:/usr/share
PKG_CONFIG_PATH=/opt/rh/rh-ruby24/root/usr/local/lib64/pkgconfig:/opt/rh/rh-ruby24/root/usr/lib64/pkgconfig:/opt/rh/rh-passenger40/root/usr/lib64/pkgconfig:/opt/rh/nginx16/root/usr/lib64/pkgconfig
(END)
```

