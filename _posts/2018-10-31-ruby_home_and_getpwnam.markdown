---
layout: post
title: "Ruby, $HOME, and getpwnam"
date: 2018-10-31T14:55:56-04:00
---

I would expect `Dir.home` to change when I change `HOME` environment variable in Ruby:

```
$ echo $HOME
/home/efranz
$ pwd
/home/efranz
$ ls -ld ~
drwx------ 2 efranz PZS0714 4096 Oct 31 14:54 /home/efranz
$ HOME=$(mktemp -d)
$ echo $HOME
/tmp/tmp.IkjRJV5IYi
$ pwd
/home/efranz
$ ls -ld ~
drwx------ 2 efranz PZS0714 4096 Oct 31 14:57 /tmp/tmp.IkjRJV5IYi
$ (ls -ld ~)
drwx------ 2 efranz PZS0714 4096 Oct 31 14:57 /tmp/tmp.IkjRJV5IYi
$ ruby -e "puts \`ls -ld ~\`"
drwx------ 2 efranz PZS0714 4096 Oct 31 14:57 /tmp/tmp.IkjRJV5IYi
$ ruby -e "puts Dir.home"
/tmp/tmp.IkjRJV5IYi
```

It does. **But not for `Dir.home(USERNAME)` or `File.expand_path`**:

```
$ ruby -e "puts Dir.home('efranz')"
/home/efranz
$ ruby -e "puts File.expand_path '~efranz'"
/home/efranz
```

What is happening? Lets peek at the implementation of `Dir.home`. _(I'm using system Ruby `ruby 2.0.0p648` on RHEL7 for this post)_

<https://ruby-doc.org/core-2.0.0/Dir.html#method-c-home>

    return rb_home_dir(u, rb_str_new(0, 0));

> Returns the home directory of the current user or the named user if given.

This is one function that executes two different code paths based on the argument provided (or omitted). See <https://www.ruby-lang.org/en/news/2015/12/16/ruby-2-0-0-p648-released/> It is actually stored in subversion, but [here is a copy in GitHub](https://github.com/ruby/ruby/tree/v2_0_0_648).

1.  If `rb_home_dir` [is called without the username](https://github.com/ruby/ruby/blob/v2_0_0_648/file.c#L2899-L2906), it returns the value of `getenv("HOME")`:
2.  If `rb_home_dir` [is called with a username](https://github.com/ruby/ruby/blob/v2_0_0_648/file.c#L2907-L2921), it gets the password struct via `getpwnam(user)`

So this can cause problems if you try to change the home directory for the Ruby process you are executing by modifying the `HOME` environment variable **and you are using RubyGems <v3** because RubyGems has an internal method `find_home` that uses `File.expand_path "~"` instead of `Dir.home`. See <https://github.com/rubygems/rubygems/commit/47c9f378687a6f806561a14dd50eae8eb4652882> which switched from using `File.expand_path "~"` to `Dir.home` to find the home directory.

See `man 3 getpwnam`

> The getpwnam() function returns a pointer to a structure containing the broken-out fields of
> the record in the password database (e.g., the local password  file  /etc/passwd,  NIS,  and
> LDAP) that matches the username name.

## Conclusion

Be careful to consider whether or not it is appropriate to use `Dir.home` or `Dir.home(user)` and treat them as two separate functions. And assume that `File.expand_path` (or basically anything that resolves `~` in Ruby) is going to use `getpwnam`.

### rubygems is now careful about this as of v3

See <https://github.com/rubygems/rubygems/commit/47c9f378687a6f806561a14dd50eae8eb4652882> which switched from using `File.expand_path "~"` to `Dir.home` to find the home directory.
