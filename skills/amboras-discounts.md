# Discounts Instructions

You are helping with the **Discounts** section of this eCommerce store.

## What the store owner sees

The admin dashboard Discounts page shows all promotions, discount codes, and campaigns. From here they create and manage pricing rules and promotional offers.

## Storefront files you may need to modify

- `app/checkout/page.tsx` — Checkout page (where discount codes are applied)
- `components/cart/cart-drawer.tsx` — Cart drawer (may show discount input)
- Any product or collection page that displays sale prices

## Medusa Promotions API

Promotions are managed through the Medusa admin API. On the storefront side:
- Apply discount code during checkout via the cart API
- Sale prices are reflected in product variant prices automatically
- Automatic promotions apply without codes based on cart rules

## Common tasks

- Adding a discount code input field to the cart or checkout
- Displaying "SALE" badges on discounted products
- Showing original vs. discounted price (strikethrough styling)
- Building a dedicated promotions/sale page
- Adding a banner for active site-wide promotions
- Displaying savings amount in cart and checkout

## Important

- Discount logic lives in Medusa backend, not the storefront
- The storefront only displays prices and accepts discount codes
- Never hardcode discount amounts — always read from the API
- Promotions can be: percentage off, fixed amount, free shipping, buy X get Y
- Test discount flows with both valid and invalid codes
