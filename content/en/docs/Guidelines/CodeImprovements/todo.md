---
title: "Nuke TODO comments"
linkTitle: "TODO comments"
weight: 200
date: 2021-03-08
description: >-
  When a todo lives in your code, it can't be prioritized or scheduled, and tends
  to get forgotten.
---

grep your codebase for TODO comments.

When you find one, I recommend you do one of the following: 

1. Is it out of date? Delete it.
2. Is it still relevant? Delete it and add it to whatever system you use to
   track work to be done, like GitHub Issues or Trello or Asana.
3. Are you unsure? Do a little research and/or track down the comment's author
   and get an answer. Then do 1 or 2 :).

Why do this? Because code is a lousy place to track todos. When a todo lives in
your code, it can't be prioritized or scheduled, and tends to get forgotten.

For extra points, submit a PR that deletes all the TODO comments and include
links to the newly-created issue for each one. Should make for an easy
review/approval.
