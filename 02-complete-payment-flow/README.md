# Understanding the Complete Payment Flow with Paystack API

## Overview
This guide walks through the complete Paystack payment flow in sequence, from initialising a transaction to verifying payment, so developers can understand how each step connects.

## Step 1: Initialise Transaction
The payment flow begins when your server creates a transaction.

This step tells Paystack:

- Who is making the payment
- How much they are to be charged
- That a payment session should be prepared

To do this, send a POST request from your backend:
```
curl -X POST https://api.paystack.co/transaction/initialize
-H "Authorization: Bearer YOUR_SECRET_KEY"
-H "Content-Type: application/json"
-d '{ "email": "testuser@email.com", 
      "amount": 500000
    }'

```

The request includes the customer's email and the amount to be charged.

When this request is sent, Paystack responds with:
```
{
  "status": true,
  "message": "Authorization URL created",
  "data": {
    "authorization_url": "https://checkout.paystack.com/...",
    "access_code": "...",
    "reference": "..."
  }
}
```
- `authorization_url` → where the customer completes payment.
- `reference` → used to verify the transaction later.
- `access_code` → used internally by Paystack to load and manage the checkout flow.

The `authorization_url` is the most important part of this response, as the customer uses it to complete the payment.

## Step 2: Redirect the Customer
The customer is redirected to the `authorization_url`.

This is where the payment happens.

Paystack handles the payment interface and security, so you don't need to build this yourself.

## Step 3: Customer Completes Transaction
Here, the customer enters their card details on Paystack's secure page.

Paystack processes the payment and may redirect the customer back to your frontend, depending on how your integration is configured.

## Step 4: Verify Transaction
After payment, your server must verify the transaction.

You should never rely on the frontend for confirmation, as this can be manipulated.

Send a request using the transaction reference:
```
curl https://api.paystack.co/transaction/verify/:reference
```

Use the `reference` returned during initialisation from step 1 to confirm that the payment was successful before granting access or delivering value.

## Putting It All Together
The flow begins when your server initialises a transaction and receives an authorisation URL.

The customer is redirected to this URL to complete payment.

After payment, your server verifies the transaction using the reference to confirm its status before taking any action.

### Common Mistakes Developers Make
- Verifying payment on the frontend.
- Not storing the transaction reference.
- Skipping the verification step.
- Exposing their secret key.
