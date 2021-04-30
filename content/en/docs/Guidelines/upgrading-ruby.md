---
title: "Upgrading Ruby"
linkTitle: "Upgrading Ruby"
date: 2021-04-29
description: >
  Steps to follow when upgrading Ruby
---

Before doing an upgrade of Ruby, evaluate which are the available versions. The
Ruby core team generally releases 3 versions at the same time. The two latest
are in "normal" maintenance mode while the last one is in "security"
maintenance mode. We should do our best effort to use one of the "normal" ones
and worst case scenario a "security" one but never a version that is already
EOL (end of life) because it can bring a security risk. Check out the currently
supported versions [here](https://www.ruby-lang.org/en/downloads/branches/).

A minor version upgrade is less risky than a major version one. If there's a
major version available for upgrade, first upgrade to the latest minor version
of the one in use, make sure everything is working as expected and then repeat
the process to go to the latest minor version of the major version you want to
run. E.g. If we're running 2.6.6 and 2.6.7, 2.7.0 ... 2.7.3 are available.
First upgrade to 2.6.7, if everything works well then do the upgrade to 2.7.3
and not to 2.7.0 because it lacks some of the security patches that 2.6.7
already includes.

1. Read the [Release Notes](https://www.ruby-lang.org/en/downloads/releases/).
   Here's you'll find out about deprecations or backwards incompatible changes.
   It's also a good place to learn about performance improvements. If there's
   anything critical, create tickets to do testing or validations. E.g. compare
   memory allocation once we're using the new garbage collector

2. Upgrade your ruby manager (RVM or rbenv and ruby-build) and install the new
   version in your local machine.
   
        rbenv install 2.6.7
      
3. Change `.ruby-version` file to match the newly installed version
4. Install gems with bundler

        bundle install
      
5. Run all automated tests and make sure they are all green

        bundle exec rspec spec/

6. Do manual tests of any of the important flows that are not covered by an
   automated test. Currently listed in
   [GitHub](https://github.com/listminut/listminutv3/projects/1)
7. Deploy in a review app and do an exploratory manual test just to see if you
   catch anything out of the ordinary.
8. Add tests of tickets for any of your findings and fixes.
9. Share to all devs (also outside of back-end team) about the change, and make
   sure to give them support on the upgrade on their local machines if need it
   be
