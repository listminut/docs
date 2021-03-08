---
title: "Create a setup script"
linkTitle: "Setup Script"
weight: 800
date: 2021-03-08
description: >-
  Know what's even better than a list of setup instructions to follow? A setup script that does them for you!
---

Here's an example I've used on Rails apps to good success:
https://gist.github.com/r00k/ab4dce37603cd94466c9955eee88ffe1

I name this script `setup` and throw it in the `bin` directory.

Ideally, this one command is all someone needs to run before developing on your
app or using your tool. But even if you can't reach this ideal, a `bin/setup`
script will save your users or team members time and frustration.

Today, please spend 20 minutes creating a similar script. You should be able to
crib heavily from the example above, but here are a few ideas for things you
might include:

- Download and install dependencies
- Create necessary databases
- Seed the database with useful data, like an admin account
- Set up useful git remotes
- Set configuration variables / copy config files 
- Print helpful info 

If you already have a setup script, try cloning your app into a new directory
and running the script. Make sure it still works, and consider if you should
add anything to it.
