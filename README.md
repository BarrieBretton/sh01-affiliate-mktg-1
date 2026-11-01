# The Object Edit

A Vue + Vite frontend for an independent product-editorial site designed to be hosted on Cloudflare Pages.

## Run locally

```bash
npm install
npm run dev
```

## Build

```bash
npm run build
```

Cloudflare Pages settings:

- Framework preset: `Vue`
- Build command: `npm run build`
- Output directory: `dist`
- Node version: 20 or newer

## Before applying to Amazon Associates

1. Replace the sample editorial copy with genuinely original articles and product-specific testing notes. Keep at least ten substantial public posts live, with several updated within the last 60 days.
2. Add an About page with the real publisher identity, a Contact page, Privacy Policy, and any required cookie/analytics disclosure.
3. Replace the preview shortlist buttons with real Associates links only after acceptance and after the correct tag has been generated for the site.
4. Add Amazon’s required site-level statement exactly as required by the current Associates Operating Agreement, plus a clear disclosure beside each monetised link.
5. Submit the exact production domain and any social pages/apps that will contain Associates links. Keep the submitted site list current.

The current preview deliberately contains no active affiliate links and does not claim an Amazon relationship.

## Interaction system

The frontend includes a scroll progress rail, active-section tracking, sticky glass header, animated ticker, pointer-context cursor on editorial cards, lightweight card tilt, image hover zoom, filter layout transitions, animated navigation/search overlays, modal story transitions, theme transitions, and hover-responsive method cards. These are intentionally CSS/DOM-native so the site stays lightweight and deployable as a static Cloudflare Pages build.
