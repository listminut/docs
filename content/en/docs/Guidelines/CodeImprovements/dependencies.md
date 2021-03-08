---
title: "Audit your dependencies"
linkTitle: "Dependencies"
weight: 1200
date: 2021-03-08
description: >-
  Unmanaged dependencies are a burden and can make a project slow or can add
  unnecessary risks
---

Crack open your Gemfile, package.json, setup.py, or whatever file your
language/dependency manager uses.

Give it a slow scan. Ask yourself:

Do you still need everything in there?

Does anything need to be updated?

Can you reduce a production dependency to a development/test one?

Rubyists: maybe run [bundler-audit](https://github.com/rubysec/bundler-audit)
to automatically check for gems with known vulnerabilities.

Is your file nicely laid out and sorted alphabetically? Should it be?
