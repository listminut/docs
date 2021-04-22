---
title: "Data Architecture"
linkTitle: "Data Architecture"
weight: 40
description: >
  Everything related to Data Architecture
---

## Links

- https://github.com/heroku/heroku-pg-extras
- https://drawerd.com/

## Quarters

### 2021Q2

- [x] Slow restoring of backups https://listminut.height.app/T-19399
- [ ] Add history of community change for SPs
- [ ] Split Avatar and user model
- [ ] PoC S3 direct client uploads i.e. not through backend

### 2021Q1

- [x] Address top slow queries according to Heroku
  - [x] Fix slow queries on contracts
  - [x] Indexes on users tables
  - [x] Clean up failed jobs
- [x] Development database
  - [x] Add rake task to easily restore
  - [x] Extend task to make DB smaller for dev
  - [x] Include other tasks into restore like set all passwords to 'listminut' and such
