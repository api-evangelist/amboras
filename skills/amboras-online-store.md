# Online Store Instructions

You are helping with the **Online Store** section — the storefront design and customization.

## What the store owner sees

The admin dashboard Online Store page is the storefront customization hub. From here they manage the look and feel of their customer-facing website: themes, pages, navigation, and layout.

## Storefront files you will commonly modify

### Layout & Navigation
- `app/layout.tsx` — Root layout (global wrapper, providers, fonts)
- `components/layout/header.tsx` — Site header (logo, navigation, cart icon)
- `components/layout/footer.tsx` — Site footer (links, copyright, social)
- `components/layout/announcement-bar.tsx` — Top banner (sales, announcements)

### Pages
- `app/page.tsx` — Homepage (hero, featured products, collections)
- `app/about/page.tsx` — About page
- `app/contact/page.tsx` — Contact page
- `app/faq/page.tsx` — FAQ page
- `app/privacy/page.tsx` — Privacy policy
- `app/terms/page.tsx` — Terms of service
- `app/shipping/page.tsx` — Shipping info
- `app/not-found.tsx` — 404 page

### Marketing & Homepage
- `components/marketing/collection-section.tsx` — Featured collection on homepage

### Global Styles
- `tailwind.config.ts` — Theme colors, fonts, spacing
- `app/layout.tsx` — Font imports, global CSS

## Common tasks

- Changing the site color scheme (edit `tailwind.config.ts` theme.extend.colors)
- Updating the header navigation links
- Redesigning the homepage hero section
- Adding new sections to the homepage (testimonials, newsletter, Instagram feed)
- Customizing the footer layout and links
- Changing fonts (import in layout.tsx, add to tailwind config)
- Adding new static pages (create `app/{page-name}/page.tsx`)
- Modifying the announcement bar content
- Adding social media links
- Improving mobile responsiveness

## Important

- All styling is Tailwind CSS — no custom CSS files
- The homepage is the most important page for conversion
- Always ensure changes are mobile-responsive (test at 375px width)
- Use Framer Motion for animations (already installed)
- Use Lucide React for icons (already installed)
- Keep page load fast — avoid large images, use next/image when possible
- The header and footer appear on every page via the root layout
