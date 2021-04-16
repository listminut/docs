---
title: "Investigate long parameter lists"
linkTitle: "Long Parameters"
toc_hide: true
weight: 1300
date: 2021-03-08
description: >-
  Should any of the data that you're passing in be instance data instead?
---

Do a search through your project for long parameter lists, where long is
defined as more than three parameters.

Due to long parameter lists often being line-wrapped, you’ll likely have to do
some manual searching, but here's a naive regular expression that will find
single-line method calls (in Ruby, at least) with at least four parameters:

```
(.*,.*,.*,.*)
```

You’ll probably find that many of your long parameter lists appear when calling
library or framework code.

However, you might find some in your own code. If you do, it doesn't
necessarily mean there's anything wrong, but it's worth asking yourself a few
questions about them:

Should any of the data that you're passing in be instance data instead? A clear
indication this is true is if other methods on this object also require the
same parameter. Do you frequently pass several of these parameters together? If
so, it's possible you have a [Data
Clump](https://martinfowler.com/bliki/DataClump.html) and could benefit from
extracting a value object to contain them. Are any of these parameters
booleans? If so, you probably have a case of [control
coupling](https://thoughtbot.com/blog/types-of-coupling#control-coupling), and
would do well to remove it. Can any of these parameters be removed outright?
You'd be surprised how easy it is to continue passing something into a method
that no longer requires it. By the way, the above questions are worth asking
about just about any list of parameters, so consider them even if you tend to
keep your parameter lists short.
