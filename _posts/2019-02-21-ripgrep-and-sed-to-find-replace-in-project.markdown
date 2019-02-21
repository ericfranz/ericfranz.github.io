---
layout: post
title: "Using ripgrep and sed to Find Replace in Project"
date: 2019-02-21T11:35:36-05:00
---

Example. In a documentation project I have used the word "till" instead of "until".

Using Ripgrep I find the locations this occurs:

```
$ rg '\btill'
source/release-notes/v1.5-release-notes.rst
247:till all the clusters are queried. This takes advantage of the ability to read

source/release-notes/v1.4-release-notes.rst
26:   we recommend that you wait till the 1.5 release to upgrade.
```

* `egrep '\btill' . -R` is the same as `rg '\btill'`

Then I list these as just the files

```
$ rg '\btill' -l
source/release-notes/v1.5-release-notes.rst
source/release-notes/v1.4-release-notes.rst
```

Then I pipe to sed to replace "till" with "until":

```
$ rg '\btill' -l | xargs sed -i 's/\btill/until/g'
$
```

The resulting diff:

```diff
diff --git a/source/release-notes/v1.4-release-notes.rst b/source/release-notes/v1.4-release-notes.rst
index 1600716..b61c12b 100644
--- a/source/release-notes/v1.4-release-notes.rst
+++ b/source/release-notes/v1.4-release-notes.rst
@@ -23,7 +23,7 @@ Highlights in 1.4:

    We are actively developing this month and will be releasing 1.5 at the end of
    the month. Unless you want access to some of the features listed below now,
-   we recommend that you wait till the 1.5 release to upgrade.
+   we recommend that you wait until the 1.5 release to upgrade.


 Upgrading from v1.3
diff --git a/source/release-notes/v1.5-release-notes.rst b/source/release-notes/v1.5-release-notes.rst
index 80af014..664af75 100644
--- a/source/release-notes/v1.5-release-notes.rst
+++ b/source/release-notes/v1.5-release-notes.rst
@@ -244,7 +244,7 @@ Performance Improvements to Active Jobs

 When displaying jobs from all clusters in Active Jobs, the app will now stream
 and display the job data for each cluster as it is retrieved, instead of waiting
-till all the clusters are queried. This takes advantage of the ability to read
+until all the clusters are queried. This takes advantage of the ability to read
 portions of an XHR response before the response has completed, which means you
 will only see the benefits in Chrome and Firefox.
```

This ease of progressively identifying all the locations you want to change,
prior to applying the change, makes this a superior method to other approaches
of finding and replacing a string, unless you are using a smart IDE with
refactoring capabilites.
