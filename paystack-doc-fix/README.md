# Paystack API - Before → After Documentation Fix

## Overview
The [Initialise](https://paystack.com/docs/payments/accept-payments/) section of the Paystack API website has several issues that make it harder for developers to understand and implement correctly.
These problems are identified here, with a clearer fix rewritten, and an explanation for why  the improved version is better.

## The Problem
1. The section presents a weak mental model.

The documentation says:
   > “Initialise the transaction from your backend”

It doesn’t clearly answer:

- What is initialising?
- What does it actually do in the payment flow?

This can be difficult for a beginner to grasp, leaving them without a clear understanding of the step.

2. It jumps into code too fast

The code is introduced too quickly, going straight to:
  ```
  curl https://api.paystack.co/transaction/initialize
  ```
Without clearly explaining:

- What this request represents
- What the developer is trying to achieve

This can quickly increase cognitive load.

3. Incomplete explanation of the response

It says:
   > “access_code parameter... needed to complete the transaction”

This:
- Doesn't explain how it is used
- Doesn't mention the authorization_url (which is actually critical for completing the payment)

4. The flow is incomplete

The developer doesn't see the full picture or what happens after initialisation.

What should have been said:

   > “After this → redirect user → payment happens”

5. The warning is disconnected

The “Don’t use your secret key…” section:

   - Feels abrupt
   - and is not integrated into the flow

This feels poorly placed.

## The Rewrite
Before a customer can make a payment, you need to create a transaction.

When you are initialising a transaction, you are telling Paystack:

- Who is making the payment
- How much they should be charged
- To prepare a payment session for them

Follow these steps from your backend to keep your secret key secure.

## Step 1: Send a request to Paystack

To initialise a transaction, send a POST request to Paystack’s API from your server:

```
curl -X POST https://api.paystack.co/transaction/initialize \
  -H "Authorization: Bearer YOUR_SECRET_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "email": "customer@email.com",
        "amount": 500000
      }'
```

This request creates a new transaction for the specified customer and amount.

## Step 2: Handle the response

Paystack returns a response containing important details about the transaction.

Inside the `data` object, you’ll find:

- `authorization_url`: This is the link where the customer completes the payment.
- `reference`: A unique ID for tracking the transaction.
- `access_code`: Used internally to verify and manage the transaction.

## Step 3: Redirect the customer

To complete the payment, redirect the customer to the `authorization_url`.

This takes them to Paystack’s secure payment page, where they can enter their payment details.

### Important: Keep your secret key secure

Always initialise transactions from your backend.

Never call Paystack’s API directly from your frontend, as this would expose your secret key. Instead, your frontend should communicate with your server, which then makes the request to Paystack.


## Why This Version is Clearer and Better
Unlike Paystack's, this version:
- Introduces a clear mental model before showing code.
- Explains what “initialise” means in practical terms.
- Breaks the process into logical steps.
- Clearly shows what happens after the API call.
- Explains key response fields in context.
- Integrates security guidance naturally into the flow.
