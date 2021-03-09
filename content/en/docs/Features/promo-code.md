---
title: "Promo Codes & Credits"
linkTitle: "Promo Codes & Credits"
date: 2021-03-09
weight: 100
description: >-
     Documentation about promo codes and credits
---

## Terminology

- **user_category** : Can be used by Clients (deduce price from tasks) or SPs (deduce from service fee)
- **user_type** : Can be used by new users (never closed a task as client nor as SP) or all clients
- **cumulable** : Can be used together with other credits ?

## General

Promo codes & credits are 90% the same thing (and they work the same way, use the same methods). The only real difference is that credits can be used little by little, where a promo code has to be used in one time. So, if you have 100€ credit, you can use it to pay 5 20€ tasks, but if you have one 100€ promo code, it will be used in one time, so at the first 20€ task your promo code will be consumed. Other differences are mostly semantic. A promo code will usually be non-cumulable, where a credit will almost always be cumulable, but this isn't set in stone as we can always change the cumulable field.

## Rules

We first use promo codes (first use promo code with highest amount, then closest expiry date, then code that was added first)

Then we use credits (first use credit with closest validity_date, then follow this priority for tr_type: solde, operations credits, gift card credits, partnership, parrainage)

## Models

Two models are being used : `Code` & `Transaction`.

`Code` is a promo code that can be used by users. It isn't linked to a specific user, it just holds information about the promo code (example: "CHRISTMAS", 10€, not cumulable).

`Transaction` is a specific transaction for one specific user. We only use the `Transaction` model for credits / promo codes, not for money-related things. Transactions can be positive or negative (using the positive:boolean field). A positive transaction means some credit is being added to the user account, a negative transaction means credit is being used.
Examples of transaction can be: "User x added the CHRISTMAS promo code for 10€ - not cumulable", "User x got 10€ cumulable credit", "User x used 10€ credit for task 123"

A positive transaction has `amount_used`, which keeps track of how much credit has already been used from the original amount. (You can use `Transaction#credit_left` to know how much credit is left for a transaction). `amount_used` is automatically updated everytime a new `Transaction` is created (in an after_create callback).

A negative transaction has a `transaction_id` which is the id of the positive transaction from which we are using some/all of the credit.

A transaction can also have a `code_id` if it is linked to a promo code.

We also have 3 fields on the `User` model: `solde_amount`, `marketing_amount`, `deduction_amount`. You can use `User#credit_amount` to have the total of all 3 fields. These fields are from the old system, but they are used in so many places, we can't get rid of them. So we don't use them for any calculations, but we need to keep those fields up to date. They are automatically updated everytime a new `Transaction` is created (in an after_create callback).

## How it works

### For clients

Everything is mostly calculated in `PriceComputerForSubswitch`: `used_promo_codes`, `promo_code_amount`, `credit_amount`
These methods don’t change anything in the db, they just calculate how much the client will deduce from his credits.
But the result is used in `Payment#recompute_before_closing` to save the important parts on the `Payment` object.
This info is then displayed on `contracts/payments/new`.

The actual db change for transactions happens in `User#pay_task` (in `has_credits.rb`)
This will create negative transactions to deduce the credit/promo codes. 
Note: The name might be a bit confusing because this method doesn’t actually “pay” , it just uses the credits/promo codes, but it’s called at the moment of paying the task.

### For SPs

We save `bonus` on the `Payment` model, this represents the total amount of credits + promo codes that will be used to deduce from the service fee.

Everything mostly happens in `ValidatePayment.call` (in `calculate_bonus_amount` and `create_bonus_credit_transaction`) and re-uses the same methods as for clients (`User#pay_task`).

We also calculate and save `Payment.bonus` in `AdminController#payments` for "virements".

We also call `User#pay_task` in `StripeSessionCompleted#create_bonus_credit_transaction`.

## Automatic processes

`Transaction#amount_used` is automatically updated in an after_create callback (in the `Transaction` model).

`User#credit_amount` is automatically updated in an after_create callback (in the `Transaction` model).

`Transaction#valid_date` and `Code#expiration_date` are automatically set to `end_of_day` in a before_save callback. This is so when we show a code expiring on 22/02, it can still be used on 22/02, otherwise customers might complain. If you want to remove this logic, you need to make sure everywhere in the code we only compare dates (with <= and not just <) and never compare Time, otherwise your code might expire on 22/02 at 13:00 and the customer can’t use the code on 22/02 at 14:00.

There is a cron job that will check all expired transactions and create a negative transaction with a text explaining the transaction expired. That should also trigger the update of `amount_used`, so the rest of the code will know not to take this transaction into account anymore.

