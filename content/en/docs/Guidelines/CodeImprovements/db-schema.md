---
title: "Audit your DB schema"
linkTitle: "DB Schema"
weight: 1500
date: 2021-03-08
description: >-
  Keeping your DB well groomed will save you a lot of headaches
---

Some things you might look for:

- Inconsistent column names.
- Missing indices for columns you frequently query by.
- Missing unique indices to ensure uniqueness.
- Missing null constraints.
- Missing foreign key constraints.
- Use the [bullet](https://github.com/flyerhzm/bullet) gem to detect N+1
  queries in `activerecord` and `mongoid`.
