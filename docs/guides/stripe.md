---
title: Integrating Stripe with Nitric
description: Build a serverless application with Stripe and Nitric for AWS, Google Cloud, or Azure
---

## What we'll be doing

Creating a Stripe checkout backed by a Nitric API and collections. This API will be completely compatible with Stripe's checkout components, and can be deployed to the cloud of you choice.

1. Add stripe to the project
2. Create handler for checkout sessions
3. Create a webhook for dealing with stripe events
4. Run locally for testing
5. Deploy to a cloud of your choice

## Prerequisites

- [Node.js](https://nodejs.org/en/download/)
- The [Nitric CLI](https://nitric.io/docs/installation)
- An [AWS](https://aws.amazon.com), [GCP](https://cloud.google.com) or [Azure](https://azure.microsoft.com) account (_your choice_)
- A [Stripe](https://dashboard.stripe.com/login) account

## Getting started

We’ll start by creating a new project for our API.

```bash
nitric new
```

Create a project name, select the TypeScript template and choose the default glob handler.

```bash
? What is the name of the stack? nitric-stripe
? Choose a template: official/TypeScript - Starter
? Glob for the function handlers? functions/*.ts
```

Next, open the project in your editor of choice.

```bash
code nitric-stripe
```

Make sure all dependencies are resolved with npm.

```bash
npm install
```

The scaffolded project should have the following structure:

```text
+--functions/
|  +-- hello.ts
+--node_modules/
|  ...
+--nitric.yaml
+--package.json
+--README.md
```

## Add stripe to your project

We will need the stripe module from npm to build the stripe backend.

```bash
npm install stripe
```

We will also add a utils file for our stripe object and some environment variables. We'll put ours in `common/utils.ts`

```ts
import Stripe from 'stripe';
import dotenv from 'dotenv';

dotenv.config();

export const stripeWebhookSecret = process.env.STRIPE_WEBHOOK_SECRET;

export const stripe = new Stripe(process.env.STRIPE_API_KEY, {
  apiVersion: '2020-08-27',
  appInfo: {
    name: 'Nitric Stripe Integration',
    version: '0.1.0',
  },
});
```

As we will be storing our environment variables in a `.env` file, we will use the `dotenv` module from npm.

```bash
npm install dotenv
```

Using `dotenv.config()` will load the environment variables into `process.env` so we can use them in the code without directly hard coding it.

The Stripe webhook secret is for verifying that events are coming from stripe and not a third party. The Stripe object is for using the Stripe API under the hood.

## Create handler for checkout sessions

We'll first create a handler for creating checkout sessions. We will define a `POST` route, where a request containing a list of price ids and quantities will return a checkout session url. You can then redirect the user to the session.

There is also an optional choice for a success and cancel url for where to redirect the user after the checkout has been completed.

```typescript
import { api } from '@nitric/sdk';
import { stripe } from '../common/utils';

const paymentApi = api('payments');

interface Item {
  price: string; // The price id
  quantity: number;
}

type CheckoutRequest = { items: Item[] };

paymentApi.post('/create-checkout-session', async (ctx) => {
  const { items } = ctx.req.json() as CheckoutRequest;

  try {
    const session = await stripe.checkout.sessions.create({
      line_items: item,
      mode: 'payment',
      success_url: `{YOUR_DOMAIN}/success?session_id={CHECKOUT_SESSION_ID}`,
      cancel_url: `https://{YOUR_DOMAIN}/cancel`,
    });
    ctx.res.body = session.url;
    return ctx;
  } catch (err) {
    ctx.res.body = `An error occurred: ${err}`;
    ctx.res.status = 500;
    return ctx;
  }
});
```

## Create webhook for stripe events

Creating a webhook that connects to stripe means that you can have reactive logic for when certain events occur. For this example, we are going to only handle `checkout.session.completed` events. However, there is a full list of event types [here](https://stripe.com/docs/api/events/types).

This route is simple, but quite long. We will break it down, but here is the full example:

```typescript
import { api } from '@nitric/sdk';
import Stripe from 'stripe';
import { stripe, stripeWebhookSecret } from '../common/utils';

...

paymentApi.post('/webhook', async (ctx) => {
    const buf = Buffer.from(ctx.req.data);
    const sig = ctx.req.headers["stripe-signature"];
    let event: Stripe.Event;

    try {
        if (!sig || stripeWebhookSecret) {
            ctx.res.status = 400;
            ctx.res.body = "No sig or webhook secret";
        }
        event = stripe.webhooks.constructEvent(buf, sig, stripeWebhookSecret)
    } catch (err) {
        ctx.res.status = 400;
        ctx.res.body = `Webhook Error: ${err.message}`;
        return ctx;
    }

    try {
        switch (event.type) {
            case "checkout.session.completed":
                ctx.res.status = 200;
                ctx.res.body = `Successfully completed checkout`
                handleOrder(event.data);
                break;
            default:
                throw new Error(`Unhandled event of type: ${event.type}`);
        }
    } catch (err) {
        console.log(err);
        ctx.res.status = 400;
        ctx.res.body = 'Webhoook error: "Webhook handler failed. View logs"';
    }
    return ctx;
});

const handleOrder = (session: Stripe.Event.Data) => {
  //Fill this in for handling the order.
}
```

The first step is to verify that the event signature is correct, thus validating that the event came from Stripe. We extract the stripe-signature from the request headers, then compare the signature against our Stripe webhook secret. The webhook secret comes from the environment variables and can be set when we start testing. We use the `stripe.webhooks.constructEvent` function for doing this comparison. It will throw an error if it runs into an error.

```typescript
const buf = Buffer.from(ctx.req.data);
const sig = ctx.req.headers['stripe-signature'];
let event: Stripe.Event;

try {
  if (!sig || stripeWebhookSecret) {
    ctx.res.status = 400;
    ctx.res.body = 'No sig or webhook secret';
  }
  event = stripe.webhooks.constructEvent(buf, sig, stripeWebhookSecret);
} catch (err) {
  ctx.res.status = 400;
  ctx.res.body = `Webhook Error: ${err.message}`;
  return ctx;
}
```

After the event is constructed, we can write the logic to handle it based on the type. This will compare the event type in a switch statement. In this case, we only have the event type `checkout.session.completed`, but this is where you would put further event handling features.

```typescript
try {
  switch (event.type) {
    case 'checkout.session.completed':
      ctx.res.status = 200;
      ctx.res.body = `Successfully completed checkout`;
      handleOrder(event.data);
      break;
    default:
      throw new Error(`Unhandled event of type: ${event.type}`);
  }
} catch (err) {
  console.log(err);
  ctx.res.status = 400;
  ctx.res.body = 'Webhoook error: "Webhook handler failed. View logs"';
}
return ctx;
```

Once we know its a checkout completed event, the session data is passed to the `handleOrder` function to fulfill the order. This part will be very dependent on your particular business case.

```typescript
function handleOrder() {
  // Fulfill the order however you want. Some ideas are:
  // 1. Add the order to a Nitric collection. https://nitric.io/docs/collections
  // 2. Send data down your shipping pipeline.
  // 3. Send the user a notification. https://nitric.io/docs/guides/twilio
}
```

## Run it!

Now that you have an API defined with a handler and the webhook, it's time to test it out locally.

Test out your application with the `npm run dev` command:

```bash
npm run dev
```

> _Note:_ the `dev` script in the template starts the Nitric Server using `nitric start` and runs your functions.

Pressing `ctrl + a + k` will end the application.

### Testing the Checkout Session

If we want to test a checkout session, we can use any cURL, postman, or any other HTTP client.

We first need to create a product in Stripe. This will create a price_id which we can then use in the following command:

```bash
curl -X POST \
localhost:9001/apis/payments/create-checkout-session \
-d '{"items": [{ "price": "price_<ID>", "quantity": "1" }]}'
```

This will return a stripe checkout url. Once you navigate to this page, you will have a checkout. Enter the [test credit card](https://stripe.com/docs/testing) details and it will redirect you to the success url you specified when constructing the checkout session.

### Testing the Stripe Webhook

We can use the Stripe CLI for testing the webhook works. Run the following command for forwarding all stripe events to your API endpoint:

```bash
stripe listen --forward-to localhost:9001/apis/payments/webhook
Ready! You are using Stripe API Version [2020-08-27]. Your webhook signing secret is whsec_<UNIQUE_ID> (^C to quit)
```

We can then trigger events in a separate terminal, using:

```bash
stripe trigger checkout.session.completed
```

We should then see in our `strip listen` terminal logs that it has received requests.

## Deploy to the cloud

Once it has been tested, you can start deploying to the cloud.

Setup your credentials and any other cloud specific configuration:

- [AWS](/docs/reference/aws)
- [Azure](/docs/reference/azure)
- [GCP](/docs/reference/gcp)

Create a stack - a collection of resources identified in your project which will be deployed.

```bash
nitric stack new
```

```
? What do you want to call your new stack? dev
? Which Cloud do you wish to deploy to? aws
? select the region us-east-1
```

To deploy the stack run the following command:

```bash
nitric up
```

To undeploy run the following command:

```bash
nitric down
```
