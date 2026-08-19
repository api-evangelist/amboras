# Storefront

Medusa v2 eCommerce storefront. Next.js 15 App Router, React 19, Tailwind CSS, Medusa JS SDK. Multi-tenant via `X-Store-Environment-ID` header.

## Tech Stack

- **Framework**: Next.js 15 (App Router, Server Components by default, Turbopack dev)
- **Styling**: Tailwind CSS — no custom CSS unless absolutely necessary
- **Data**: Medusa JS SDK via `lib/medusa-client.ts` (singleton, auto-attaches store headers)
- **State**: TanStack Query (5-min staleTime, no refetch on window focus)
- **Forms**: React Hook Form + Zod validation
- **Payments**: Stripe Connect (`react-stripe-js`, `@stripe/stripe-js`)
- **Icons**: Lucide React
- **Animations**: Framer Motion
- **Toasts**: Sonner (bottom-right)
- **Themes**: next-themes (class-based, light default, system-aware)

## Project Structure

```
app/
  layout.tsx              # Root layout: fonts, metadata, providers, header/footer shell
  providers.tsx           # ThemeProvider + QueryClientProvider
  globals.css             # CSS variables (colors, spacing), base styles
  page.tsx                # Homepage (client) — hero, collection sections, newsletter
  products/
    page.tsx              # Product listing (client) — category filter, sort, ProductGrid
    [handle]/page.tsx     # Product detail (SERVER) — SSR product + compare-at prices
  collections/
    page.tsx              # Collections listing (server)
    [handle]/page.tsx     # Collection detail (server) — filtered ProductGrid
  checkout/
    page.tsx              # Multi-step checkout (client) — info → shipping → payment
    success/page.tsx      # Order confirmation (client)
  account/
    page.tsx              # Account overview (client, auth-protected)
    orders/page.tsx       # Order history (client)
    addresses/page.tsx    # Address management (client)
    profile/page.tsx      # Profile settings (client)
  auth/
    login/page.tsx        # Login form (client)
    register/page.tsx     # Registration form (client)
    forgot-password/      # Password reset
  search/page.tsx         # Search results (client)
  about/, contact/, faq/, privacy/, terms/, shipping/  # Static content pages

components/
  layout/
    header.tsx            # Sticky header, nav, mobile menu, search/account/cart icons
    footer.tsx            # 4-column footer, links, cookie management
    announcement-bar.tsx  # Dismissible top banner ("Free shipping over $75")
  product/
    product-card.tsx      # Card: image, title, price + compare-at strikethrough
    product-grid.tsx      # Grid: fetches products + compare-at prices, sorting, skeletons
    product-actions.tsx   # Variant selector, quantity, add-to-cart, stock status
    product-accordion.tsx # Description, shipping/returns, care instructions
    add-to-cart.tsx       # Simplified add-to-cart (used outside detail page)
  cart/
    cart-drawer.tsx       # Slide-in drawer: items, quantity edit, remove, subtotal, checkout CTA
  checkout/
    stripe-payment-form.tsx  # Stripe Elements wrapper, PaymentElement, confirmPayment
  account/
    account-layout.tsx    # Auth-protected wrapper, sidebar nav, breadcrumbs
  marketing/
    collection-section.tsx  # Homepage collection block with ProductGrid
  analytics-provider.tsx  # Consent-based page view tracking
  cookie-consent.tsx      # Cookie consent banner (accept/decline)
  element-picker-listener.tsx  # Admin dashboard element picker integration

lib/
  medusa-client.ts        # Medusa SDK singleton (baseUrl, publishableKey, store headers)
  analytics.ts            # AnalyticsTracker: session, pageview, cart, checkout, purchase events
  cookie-consent.ts       # Cookie read/write helpers (amboras_consent cookie, 1yr max-age)
  utils/
    placeholder-images.ts # Deterministic product image fallbacks

hooks/
  use-auth.ts             # Login, register, logout, customer retrieval
  use-cart.ts             # Cart CRUD (localStorage cart ID, create/add/update/remove items)
  use-checkout.ts         # Multi-step checkout state machine + Stripe init
  use-collections.ts      # Collection list fetch
  use-products.ts         # Product list fetch (with calculated_price)
  use-product.ts          # Single product fetch by handle
  use-region.ts           # Region fetch (required for pricing context)
  use-stripe-config.ts    # Stripe Connect config fetch (/store/stripe-connect)

types/
  index.ts                # Product, ProductVariant, Cart, LineItem types
  browser.d.ts            # requestIdleCallback, NetworkInformation type extensions
```

## Root Layout (`app/layout.tsx`)

Wraps every page. Render order:

1. **Fonts** — Cormorant Garamond (heading, serif) + DM Sans (body, sans-serif) via `next/font/google`. CSS vars `--font-heading`, `--font-body` on `<html>`.
2. **Providers** — ThemeProvider + QueryClientProvider
3. **ElementPickerListener** — admin dashboard integration
4. **AnnouncementBar** — dismissible top banner
5. **Header** — sticky nav, logo, search, account, cart
6. **main** > **AnalyticsProvider** > `{children}`
7. **Footer**
8. **CookieConsent** — bottom banner
9. **Toaster** — Sonner notifications

To change fonts: edit import + config in `layout.tsx` lines 3, 13-25. Tailwind maps them in `tailwind.config.ts` under `fontFamily.heading` / `fontFamily.body`.

## Data Fetching Patterns

### Server Components (SSR)
Product detail and collection pages fetch data server-side with async functions:
```typescript
// Direct SDK call in async server component
const response = await medusaClient.store.product.list({ handle, region_id, fields: '*variants.calculated_price' })
```

### Client Components (TanStack Query)
Product listing, cart, auth use hooks backed by `useQuery`/`useMutation`:
```typescript
// All hooks require regionId for pricing context
const { data: products } = useProducts({ limit: 100, category_id })
```

### Compare-at Prices (Custom Endpoint)
Not in the Medusa SDK — fetched via direct HTTP to `/store/product-extensions/products/{id}/variants`. **Must include both headers**: `X-Store-Environment-ID` and `x-publishable-api-key`.

## Pricing

- All prices are in **cents** (divide by 100 for display)
- `calculated_price.calculated_amount` — current price (includes region pricing, taxes, discounts)
- `calculated_price.currency_code` — currency (e.g. "eur")
- `compare_at_price` — original price for strikethrough display (from product-extensions endpoint)
- Strikethrough shows when `compare_at_price > calculated_amount`
- Format via `Intl.NumberFormat` with currency style

## Auth Flow

1. **Login**: `medusaClient.auth.login('customer', 'emailpass', { email, password })` → SDK stores token → fetch customer → cache → redirect `/account`
2. **Register**: `medusaClient.auth.register()` (create identity) → `medusaClient.store.customer.create()` (create customer record). On "identity exists" error: fallback to login.
3. **Logout**: `medusaClient.auth.logout()` → clear cache → redirect home

## Cart

- Cart ID stored in `localStorage` (`medusa_cart_id`)
- Auto-creates cart with first region if none exists
- Operations: `createLineItem`, `updateLineItem`, `deleteLineItem`
- `deleteLineItem` returns `response.parent` (not `response.cart`)
- Cart drawer slides in from right, shows items with quantity controls

## Checkout Flow

1. **Info** — email + shipping address → `cart.update()`
2. **Shipping** — select method → `cart.addShippingMethod()`
3. **Payment** — init payment session (`pp_stripe-connect_stripe-connect` or `pp_system_default`) → Stripe Elements form or direct "Place Order"
4. **Complete** — `cart.complete()` → redirect to `/checkout/success?order_id=...`

## Theming (CSS Variables)

Defined in `globals.css`, consumed by `tailwind.config.ts`:

| Variable | Light | Purpose |
|----------|-------|---------|
| `--background` | 40 20% 98% (cream) | Page background |
| `--foreground` | 0 0% 10% (near-black) | Text color |
| `--muted` | 40 10% 94% | Subtle backgrounds |
| `--muted-foreground` | 0 0% 45% | Secondary text |
| `--accent` | 35 40% 50% (warm brown) | Accent color, focus rings |
| `--border` | 40 10% 88% | Border color |
| `--destructive` | 0 72% 51% (red) | Error/delete actions |

Dark mode inverts backgrounds/foregrounds. Toggle via `next-themes` (class-based).

## Custom Tailwind Utilities

- `font-heading` / `font-body` — font family classes
- `text-display`, `text-h1`–`text-h4` — typography scale
- `container-custom` — `mx-auto max-w-[1280px]` with responsive padding
- `text-balance` — `text-wrap: balance`
- `link-underline` — animated underline on hover (::after)
- Animations: `fade-in`, `fade-in-up`, `slide-in-right`, `slide-out-right`

## Analytics

- Consent-based (cookie `amboras_consent`)
- Session tracking with 30-min timeout, heartbeat every 3-5s
- Events batched (max 20) and flushed every 3s
- Tracks: `page_view`, `add_to_cart`, `begin_checkout`, `purchase`
- Endpoint: `{MEDUSA_BACKEND_URL}/store/analytics/events`
- Silently fails — never breaks the store

## Environment Variables

```
NEXT_PUBLIC_MEDUSA_BACKEND_URL    # Medusa backend (default: http://localhost:9000)
NEXT_PUBLIC_MEDUSA_PUBLISHABLE_KEY # Store API key (required for all /store/* calls)
NEXT_PUBLIC_STORE_ID              # Multi-tenant store environment ID (optional)
NEXT_PUBLIC_ANALYTICS_ENDPOINT    # Analytics endpoint override (optional)
PORT                              # Dev server port (default: 3000)
```

## Debugging

**When the store crashes or user reports issues, check PM2 logs first.**

### Log Files (in this workspace)

```
logs/
├── storefront-pm2-out.log   ← Next.js stdout (compilation, requests, console.log)
└── storefront-pm2-error.log ← Next.js stderr (errors, stack traces)
```

### After Making Code Changes

**ALWAYS verify your changes worked:**

1. Check compilation succeeded:
   ```bash
   tail -20 logs/storefront-pm2-out.log
   # Look for: "✓ Compiled / in Xs"
   ```

2. Check for runtime errors:
   ```bash
   tail -20 logs/storefront-pm2-error.log
   # Should be empty or only show old errors
   ```

3. If you see `GET / 500` in out.log, immediately check error.log for stack trace

### Common Error Patterns

| Error in logs | Cause | Fix |
|---------------|-------|-----|
| `Export X was not found in module Y` | Import doesn't exist in package | Check package docs for correct export names |
| `Module not found: Can't resolve 'X'` | Missing dependency | Check if package is in package.json |
| `GET / 500 in Xms` | Runtime error during render | Check error.log for full stack trace |
| `ECONNREFUSED` | Can't reach Medusa backend | Check NEXT_PUBLIC_MEDUSA_BACKEND_URL in .env.local |
| Compilation hangs | Syntax error or infinite loop | Check error.log |

### Example: lucide-react Import Error

**Error:** `Export Instagram was not found in module lucide-react`

**Cause:** Icon names don't exist or changed in lucide-react version

**Fix:** Check [lucide.dev](https://lucide.dev) for correct icon names in the installed version. Don't assume icon names - verify they exist.

## Rules

- Always use Tailwind CSS for styling
- Prefer editing existing components over creating new ones
- Use Server Components by default, only add `'use client'` when needed
- Fetch data via Medusa JS SDK (`medusaClient`), never direct API calls (exception: compare-at prices, analytics, stripe-connect config)
- All `/store/*` API calls require the `x-publishable-api-key` header — the SDK handles this automatically, but direct `fetch()` calls must add it manually
- Keep changes minimal and focused
- Read a file before editing it
- Follow existing naming conventions and patterns
- Prices are always in cents — divide by 100 for display
- Cart ID lives in localStorage — never assume a cart exists, always handle creation
- Stock checks: respect `manage_inventory` flag before showing stock status
- **After ANY code change: check `logs/storefront-pm2-out.log` for "✓ Compiled" and no 500 errors**
- **If user reports issue: check `logs/storefront-pm2-error.log` FIRST before debugging**
