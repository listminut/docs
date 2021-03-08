---
title: "Run your tests with your WiFi off"
linkTitle: "WiFi Off"
weight: 900
date: 2021-03-08
description: >-
  Turns out, it's fairly easy to accidentally rely on an external service
  during test runs (I've done it many times). This won't just make your test
  suite slow, but brittle.
---

Chances are, you'll see a few cryptic failures when you try this.

In general, I recommend reaching for a tool like
[Webmock](https://github.com/bblimke/webmock) to fix issues like these.

As a bonus, Webmock has a setting to disable external web requests during test
runs. If you attempt to make a connection to the outside world, you'll get an
exception. This will prevent you from writing internet-hitting tests in the
future.
