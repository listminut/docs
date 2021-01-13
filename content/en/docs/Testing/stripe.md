---
title: "Stripe - payments flows testing"
linkTitle: "Stripe - payments flows"
date: 2021-01-12
description: >
  Guidelines on how to test Stripe API in development.
---


{{% pageinfo %}}
Note : Before going further be sure to have an account for Stripe. If not contact Scoum to receive the access that you will have to use for the login in the next steps.
{{% /pageinfo %}}

## Stripe Developer Dashboard

Stripe offers a useful dashboard for developers to manage testing datas.

You can monitor:
- Webhooks : https://dashboard.stripe.com/webhooks
- Events : https://dashboard.stripe.com/events
- Logs : https://dashboard.stripe.com/logs

## Stripe CLI

Stripe created a great CLI to help developers test on their machine.
Here is the official documentation : https://stripe.com/docs/stripe-cli

## Webhook events
Stripe uses webhooks to notify your application when an event happens in your account. Webhooks are particularly useful for asynchronous events like when a customer’s bank confirms a payment, a customer disputes a charge, or a recurring payment succeeds.
More info : https://stripe.com/docs/webhooks

To start the webhook server that will listen to all the events, you will first have to login :

```
stripe login
```

When you are logged, you can launch the server on your machine with this command :

```
stripe listen --forward-to localhost:3000/hooks/stripe_events
```

One more step, you will now need to add an ENV variable in your file : local_env.yml.

```
STRIPE_WEBHOOK: 'whsec_XXXXXXXXXXXXXXXXXX'
```

You can find the correct whsec_XXXXXXXXXXXXXXXXXX by looking the Stripe server logs you just started.
```
Ready! Your webhook signing secret is whsec_XXXXXXXXXXXXXXXXXX
```

Don't forget to restart your Ruby server + Stripe server and you are now ready for testing :-)


## Testing Bancontact flow

How it works : _During the Bancontact payment process, a Source object is created and your customer is redirected to their bank’s website or mobile application to authorize the payment. After completing this, your integration uses the source to make a charge request and complete the payment._

When the webhook server is ready to listen, you can start testing the Bancontact flow and just follow the steps on the ListMinut application.
Here is the documentation to know more about Stripe Bancontact : https://stripe.com/docs/sources/bancontact

One example flow for Bancontact payment :
> Take a yearly subscription as a customer, you will be redirected to a fake Bank page, you can confirm the transaction and you will come back to the ListMinut application on a success page
