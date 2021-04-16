---
title: "Best Practices"
linkTitle: "Best Practices"
weight: 100
description: >-
     This page includes best practice patterns or anti-patterns to help you avoid into a pitfall.
---

## Only update a model when it has changed

Updating data in a model requires a call to the DB. However, if the data being
updated, is already in the right state, this call would be a waste. You can
validate whether you need to do an update using the `changed?` method.

Instead of doing this:

```
one = 1
two = 2
@my_model.update(one: one, two: two)
```

Do this:

```
@my_model.one = 1
@my_model.two = 2
@my_model.save if @my_model.changed?
```
