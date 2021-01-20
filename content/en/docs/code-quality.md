---
title: "Code Quality"
linkTitle: "Code Quality"
weight: 100
description: >-
     For now just a place to keep track of quality improvements we've talked about but haven't had time to address
---

## Data

* There are models where the saved data is an integer so we constantly have two methods to access it, one to get the integer and one to get its string value e.g. `Transaction#user_type` and `Transaction#user_type_to_s`
  * This also applies to tracking of APP/WEB which is the same for multiple models
* We make associations to the country for every request even when right now we only have one country.  The initial goal is to improve performance, remove hardcoded segments of the code and as a second goal prepare LM to handle multiple countries or be used as separate applications per country
