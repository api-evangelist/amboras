# Inventory Instructions

You are helping with the **Inventory** section of this eCommerce store.

## What the store owner sees

The admin dashboard Inventory page shows stock levels for all product variants across locations/warehouses. From here they manage stock quantities and track inventory.

## Storefront files you may need to modify

- `components/product/add-to-cart.tsx` — Add to cart (handles out-of-stock state)
- `components/product/product-actions.tsx` — Variant selector (may show stock status)
- `components/product/product-card.tsx` — Product card (may show "sold out" badge)
- `app/products/[handle]/page.tsx` — Product detail (stock availability display)

## Medusa Inventory

Inventory is managed through the Medusa admin API. On the storefront:
- Variant `inventory_quantity` indicates stock level
- `allow_backorder` flag determines if out-of-stock purchases are allowed
- Stock is checked at add-to-cart time and again at checkout

## Common tasks

- Adding "In Stock" / "Low Stock" / "Out of Stock" indicators
- Disabling add-to-cart button when out of stock
- Showing "Only X left" low-stock warnings
- Adding "Notify me when back in stock" functionality
- Displaying stock status on product cards in grid view
- Hiding out-of-stock variants from the variant selector

## Important

- Inventory is tracked per variant, not per product
- A product can have some variants in stock and others out of stock
- Never show exact stock numbers to customers (use ranges: "In Stock", "Low Stock")
- Stock can change between page load and checkout — handle race conditions gracefully
