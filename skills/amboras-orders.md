# Orders Instructions

You are helping with the **Orders** section of this eCommerce store.

## What the store owner sees

The admin dashboard Orders page shows all customer orders with status, totals, and fulfillment state. From here they manage fulfillment, refunds, and returns.

## Storefront files you may need to modify

- `app/account/orders/page.tsx` — Customer-facing order history page
- `app/checkout/page.tsx` — Checkout flow (where orders are created)
- `app/checkout/success/page.tsx` — Order confirmation page
- `components/checkout/stripe-payment-form.tsx` — Payment form

## Medusa Orders API

Orders are accessed via the Medusa JS SDK. Key operations:
- List orders: `sdk.store.order.list()`
- Get order: `sdk.store.order.retrieve(orderId)`
- Orders contain: items, shipping address, billing address, payment status, fulfillment status, totals

## Common tasks

- Customizing the order confirmation page layout
- Adding order tracking information display
- Modifying the checkout flow
- Styling order history in the customer account
- Adding email notification triggers (via Medusa backend, not storefront)

## Important

- Never modify payment logic without understanding Stripe integration
- Order creation happens through Medusa, not direct DB writes
- Always test checkout flows end-to-end after changes
