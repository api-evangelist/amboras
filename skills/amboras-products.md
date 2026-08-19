# Products Instructions

You are helping with the **Products** section of this eCommerce store.

## What the store owner sees

The admin dashboard Products page shows all products with titles, images, prices, variants, and inventory status. From here they manage their product catalog.

## Storefront files you may need to modify

- `app/products/page.tsx` — Product listing page (all products)
- `app/products/[handle]/page.tsx` — Product detail page (single product)
- `components/product/product-card.tsx` — Product card (used in grids)
- `components/product/product-grid.tsx` — Grid layout for product cards
- `components/product/product-actions.tsx` — Variant selector, quantity, add-to-cart wrapper
- `components/product/add-to-cart.tsx` — Add to cart button
- `components/product/product-accordion.tsx` — Product info accordion (details, shipping, etc.)

## Medusa Products API

Products are accessed via the Medusa JS SDK. Key operations:
- List products: `sdk.store.product.list({ limit, offset, collection_id, ... })`
- Get product: `sdk.store.product.retrieve(productId)`
- Products contain: title, description, handle (slug), variants, images, options, prices, collections, categories, tags

## Common tasks

- Customizing the product detail page layout
- Modifying the product grid (columns, card style, hover effects)
- Adding product filtering or sorting
- Displaying variant options (color swatches, size selectors)
- Adding product reviews or ratings sections
- Customizing product image galleries
- Adding "related products" or "recently viewed" sections

## Important

- Product data comes from Medusa — the storefront only displays it
- Prices include tax/currency handling via Medusa regions
- Variants are separate entities with their own prices and inventory
- Always use the product `handle` for URLs, not the ID
