# Analytics Instructions

You are helping with the **Analytics** section of this eCommerce store.

## What the store owner sees

The admin dashboard Analytics page shows traffic metrics (sessions, page views, unique visitors, avg duration), a conversion funnel, sessions-over-time chart, geo map with live visitors, and top pages/referrers/locations tables.

## Storefront files you may need to modify

Analytics events are tracked on the storefront and sent to the Medusa backend:

- `lib/analytics.ts` — Core analytics tracker (session management, event batching, heartbeat)
- `components/analytics-provider.tsx` — Wraps pages to track `page_view` events on route changes
- `components/cookie-consent.tsx` — Cookie consent banner (analytics only fires after consent)
- `lib/cookie-consent.ts` — Cookie read/write helpers (`amboras_consent` cookie)

### Event-generating pages
- `app/page.tsx` — Homepage (page view)
- `app/products/[handle]/page.tsx` — Product detail (page view)
- `app/checkout/page.tsx` — Checkout (begin_checkout event)
- `app/checkout/success/page.tsx` — Order success (purchase event)
- `components/product/product-actions.tsx` — Add to cart (add_to_cart event)
- `components/product/add-to-cart.tsx` — Add to cart button (add_to_cart event)

## Analytics event types

Events are batched (max 20) and flushed every 3 seconds to `{MEDUSA_BACKEND_URL}/store/analytics/events`:

- `page_view` — every route change (automatic via AnalyticsProvider)
- `add_to_cart` — when item is added to cart
- `begin_checkout` — when checkout page loads
- `purchase` — when order completes successfully

## Session tracking
- Session ID stored in sessionStorage
- 30-minute inactivity timeout
- Heartbeat every 3-5 seconds while tab is visible
- Device type detection (mobile/tablet/desktop)

## Rules
- Analytics silently fails — never breaks the store
- Consent-gated: no events fire until cookie banner is accepted
- Events include: session_id, visitor_id, page_url, referrer, device info
- UTM parameters (utm_source, utm_medium, utm_campaign) are captured from URL
