---
title: "Investigate high-churn files"
linkTitle: "High-churn"
toc_hide: true
weight: 1700
date: 2021-03-08
description: >-
  High-churn is generally an indicative of a class breaking the Single Responsibility Principle
---

In this case, churn refers the number of times a file has changed.

High-churn files are worth investigating. They're not necessarily bad, but they
might indicate a good refactoring opportunity.

Perhaps a file changes often because it is unclear and therefore buggy. Perhaps
it's doing too many things.

If you are using Git, you can measure how many commits there have been on each
of your files using [this
command](https://github.com/garybernhardt/dotfiles/blob/master/bin/git-churn):

```
git log --all -M -C --name-only --format='format:' "$@" \ | sort \ | grep -v '^$' \ | uniq -c \ | sort -n \ | awk 'BEGIN {print "count\tfile"} {print $1 "\t" $2}'
```

Consider saving it in an executable script. Call it git-churn, perhaps. This
will make it easier to reuse and to pass parameters.

For example, what are the top 10 files in the core of your codebase that have
changed the most in the past 3 months? Find out with:

```
git-churn --since='3 months ago' <core_of_the_app> | tail -10
```
