# Customers Instructions

You are helping with the **Customers** section of this eCommerce store.

## What the store owner sees

The admin dashboard Customers page shows all registered customers with their name, email, order count, and total spend. From here they manage customer accounts and groups.

## Storefront files you may need to modify

- `app/account/page.tsx` — Customer account dashboard
- `app/account/profile/page.tsx` — Edit profile (name, email, phone)
- `app/account/addresses/page.tsx` — Manage shipping addresses
- `app/account/orders/page.tsx` — Customer order history
- `app/auth/login/page.tsx` — Login page
- `app/auth/register/page.tsx` — Registration page
- `app/auth/forgot-password/page.tsx` — Password reset
- `components/account/account-layout.tsx` — Account section layout/navigation

## Medusa Customer API

Customers are accessed via the Medusa JS SDK. Key operations:
- Get current customer: `sdk.store.customer.retrieve()`
- Update customer: `sdk.store.customer.update({ first_name, last_name, phone })`
- List addresses: `sdk.store.customer.listAddresses()`
- Add address: `sdk.store.customer.createAddress({ ... })`
- Auth: `sdk.auth.login("customer", "emailpass", { email, password })`
- Register: `sdk.auth.register("customer", "emailpass", { email, password })`

## Common tasks

- Customizing the account dashboard layout
- Adding customer profile fields (birthday, preferences)
- Improving the address management UI
- Customizing login/register forms
- Adding social login buttons
- Building a loyalty points or rewards display
- Adding order history filters or search

## Important

- Customer auth uses Medusa's built-in auth system, not Supabase
- Passwords are managed by Medusa, never stored in the storefront
- Session tokens are handled via the Medusa JS SDK automatically
- Always handle auth errors gracefully (expired session, invalid credentials)
