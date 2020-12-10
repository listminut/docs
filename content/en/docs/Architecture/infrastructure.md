---
title: "ListMinut's Infrastructure"
linkTitle: "Infrastructure"
date: 2020-12-08
description: >
  This section summarizes all about ListMinut's infrastructure
---

{{% alert title="Warning" color="warning" %}}
This document is still a work in progress
{{% /alert %}}

ListMinut has a monolithic architecture, this is easily perceived when looking
at a blueprint of its infrastructure. At the center, is the platform as
a service (PaaS) Heroku, where the web application is deployed. Less than
a dozen of external services are integrated to enhance functionality.

![Infrastructure](/img/Infrastructure.png)

## 1. Cloudflare

Whenever a client calls ListMinut's web application or API, they go through
Clouflare, they handle DNS for listminut.be domain, and add a security layer
which helps mitigate DDoS attacks and other issues.

## 2. Heroku Router

Every request to our web application goes through Heroku's router.
Unfortunately this is a very dumb router so it redirects requests randomly
without evaluating the load of each dyno. It also makes the assumption that
requests are short-lived, so if you're doing a deployment while there are
a bunch of long lived-request, e.g. an image upload, you may experience see
a high increase in 500 errors, which can only be fixed with a reboot of the
dynos.

**Possible improvements**

- Add a [load balancer](https://elements.heroku.com/buttons/rafael-fernandes/heroku-load-balancer)
- Extract middleware into a web server (e.g. www to non-www redirects)

## 3. Heroku Dynos

Dyno is a fancy word to say server. Heroku uses a custom Linux image based on
Ubuntu LTS. All of them send performance stats to [New Relic](#8-new-relic).

**Planned improvements**

- [Upgrade Heroku Stack #701](https://github.com/listminut/listminutv3/issues/701)
- [External API resilience #957](https://github.com/listminut/listminutv3/issues/957)
- [Upgrade Ruby on Rails #700](https://github.com/listminut/listminutv3/issues/700)
- [Upgrade Ruby #699](https://github.com/listminut/listminutv3/issues/699)
- [Have the same build for all environments #411](https://github.com/listminut/listminutv3/issues/411)
- [Deployment process is taking too long #882](https://github.com/listminut/listminutv3/issues/882)
- [Configure ruby metrics in Heroku #983](https://github.com/listminut/listminutv3/issues/983)
- Evaluate if we should move to performance L dynos

### 3a. Web Dynos

Requests from a web or mobile client get redirected to one of the many web
dynos. Depending on the request, they may also interact internally with
[PostgreSQL](#4-postgresql-primary-db) and externally with one or more of the
following services: [AWS CloudFront](#5-aws-cloudfront), [AWS S3](#6-aws-s3),
[Rollbar](#9-rollbar) and [MemCachier](#7-memcachier). Additionally, a web dyno
can save a job to be run at a later time via one of our [Worker
dynos](#3c-worker-dynos).

**Planned changes**

- [Heroku's H12 long running requests #278](https://github.com/listminut/listminutv3/issues/278)
- Use the same Rollbar for all environments

**Possible improvements**

- Extract SEM pages and serve them as static pages

### 3b. One-off Dynos

We use one-off dynos for:

a. scheduled jobs, e.g. Synchronization of the mailing lists. The system that
   takes care of the timing and executing the jobs is [Heroku
   Scheduler](https://devcenter.heroku.com/articles/scheduler), which is a sort
   of cron job system with very limited capabilities but which allows you to
   easily execute any CLI command included in the Gemfile, most commonly rake
b. manually spawning a dyno via the Heroku's CLI to be able to debug or execute
   code in production

**Possible improvements**

- Switch (at least partially) to using an event-driven design instead of
  scheduled jobs to make our system more resilient

### 3c. Worker Dynos

Worker dynos are constantly pulling jobs from the queue, e.g sending
transactional emails. Jobs are saved in the same
[PostgreSQL](#4-postgresql-primary-db) DB, where the rest of data is hosted,
using [delayed job](https://github.com/collectiveidea/delayed_job/).

## 4. PostgreSQL Primary DB

**Planned  changes**

- [Upgrade PostgreSQL #691](https://github.com/listminut/listminutv3/issues/691)

**Possible improvements**

- Practice some level of data hygiene e.g. move old data out for performance,
  security and GDPR compliance
- Split user table between data generally needed and additional information.
  Most importantly the avatar relation which causes (a) the user object to be
  huge and (b) always makes a call to the S3 bucket.

### 4b. PostgreSQL Replica DB

## 5. AWS CloudFront

CloudFront is a AWS' content delivery network (CDN) they have a data center in
Belgium so all calls to objects have minimized latency.

**Possible changes**

- Keep infrastructure as code and have automated re-creation in case it's
  deleted or if it needs to be upgraded

## 6. AWS S3

We use multiple S3 buckets to save user images, PDF documents and application
assets.

**Planned changes**

- [Cleanup unnecessary files in uploads object storage
  #298](https://github.com/listminut/listminutv3/issues/298)
- Use CloudFront instead of accessing S3 objects from dynos
- Do file upload directly from web/mobile client to S3 bucket instead of doing
  them in the dynos
- Configure staging buckets secure headers just like in production

**Possible changes**

- Make backups in AWS Glazier
- Keep infrastructure as code and have automated re-creation in case it's
  deleted or if it needs to be upgraded

## 7. MemCachier

MemCachier is an offering for the memcached service. We use it to save sections
of some of our pages for faster loading times.

**Possible changes**

- Move SEM pages to be generated with a static site generator so they can be
  served via a web server or S3 plus CDN which are much more efficient for this
  than a Rails application and leave MemCachier only for sections that don't
  change constantly within the application.

## 8. New Relic

We use New Relic keep track of performance in ListMinut's application

## 9. Rollbar

All runtime errors, are sent to Rollbar.

## 10. Notifications

### 10a. MailJet

We use MailJet for transactional emails.

### 10b. Sendinblue

We use Sendinblue to keep email lists like for our Newsletter, for campaigns
and certain automated emails which can be configured by marketing team. Every
time an account's email is validated, it gets added to one or multiple lists
(if the user opts-in) and every night we sync Sendinblue lists with data in our
database.

**Planned changes**

- [Upgrade Sendinblue service to use API v3 #281](https://github.com/listminut/listminutv3/issues/281)
- [Make Sendinblue users sync more resilient #919](https://github.com/listminut/listminutv3/issues/919)

### 10c. OneSignal

We use OneSignal for push notifications

## 11. Papertrail

Papertrail is a log aggregator. It connects directly to Heroku via Logplex and
can be configured to filter certain logs. We currently only keep 30 days of
logs.

## 12. Librato

We're currently using Librato as a dashboard to show real-time metrics. It
connects directly to Heroku so it has access to dyno, router and database
metrics, but it can also be extended with custom ones.

## 13. HireFire

HireFire is an auto-scaling solution. It's currently configured to have
a min/max number of dynos depending on the time of the day and the number of
requests per minute.

**Possible improvements**

- Switch to Heroku's auto-scaling
