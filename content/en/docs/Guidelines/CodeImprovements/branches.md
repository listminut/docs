---
title: "Trim your branches"
linkTitle: "Branches"
weight: 500
date: 2021-03-08
description: Fewer branches are easier to navigate for you and everyone in the team
---

1. Run `git branch -r`. Marvel at all the old tracking branches that have been
   left in your local repo.
2. Run `git remote prune origin` to delete the local tracking branches that
   don't exist on `origin` anymore. You might want to throw a `--dry-run` on
   there to confirm that git is going to do the right thing.
3. Re-run `git branch -r`. Better, right?
4. Now that your local repo is clean, take a look at the branches on `origin`
   by running `git ls-remote --heads origin`. 
5. Delete any of your branches that are no longer needed with `git push origin --delete old_branch`.
6. Maybe bug your coworkers to do 4 and 5, too.
