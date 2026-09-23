# UrbanB2B — Enterprise UI/UX Rebuild

A ground-up redesign of [urbanb2b.com](https://urbanb2b.com) using the **exact same
text content and outbound links**, rebuilt as a statically-rendered Next.js site with a
clean, light enterprise visual system: a white canvas, light glassmorphism, a very
low-opacity paper grain, and a single aligned CTA pattern across every page.

Visual direction follows [vionsys.com](https://vionsys.com) as the design reference.

## Stack

| | |
|---|---|
| Framework | Next.js 15 (App Router, TypeScript) |
| Styling | Tailwind CSS v4 (CSS-first `@theme` tokens) |
| Fonts | Outfit (display) + Manrope (body) via `next/font`, self-hosted |
| Icons | Hand-rolled inline SVG set — no icon dependency |
| Rendering | 100% static prerender (`○ Static` on all routes) |

Runtime dependencies are `next`, `react`, `react-dom` only.

## Design system

**Canvas.** White, with `slate-50` bands alternating between sections and two very faint
cool radial washes near the top. The only inverted surface on the site is the closing
footer band (`slate-950`).

**Glass.** Light-mode glass — frosted *white* fills with a white inset top highlight and
soft **blue-tinted** shadows rather than black ones. Two tiers in `app/globals.css`:

- `.glass` — `rgba(255,255,255,0.55)` over `blur(20px) saturate(140%)`, for cards sitting
  on tinted bands.
- `.glass-bordered` — a slightly more opaque fill with a `slate-200` edge, for cards on
  pure-white sections where white-on-white would lose its boundary.

`.card-lift` adds a shared 3px hover rise, shifting the border to `blue-200`.

**Noise.** A fixed `feTurbulence` SVG tile at **2.2% opacity** with `mix-blend-mode:
multiply`. On a light canvas this reads as paper tooth — it exists to stop the wide
gradient washes banding, not as a texture effect.

**Type.** Outfit for headings, buttons and labels; Manrope for body copy. Headings are
**medium weight (500)**, not bold — `slate-950` with tight negative tracking (`-0.045em`
to `-0.05em`) and `1.1` leading. Body is `slate-600` at 15–17px with `1.65` leading. The
restraint in weight is what keeps the page feeling clean at large display sizes.

**Buttons.** Primary is a gradient blue pill (`#5BB5FF → #3B82F6 → #2563EB`) carrying two
inset highlights plus a blue drop shadow, with a hairline light streak across its top
edge. Secondary is a white pill with a `slate-300` border that warms to `blue-300` on
hover. Both lift 2px.

**Eyebrows.** Small Outfit labels flanked by thin `slate-300` rules — the reference's
section-marker pattern.

## Layout & CTA discipline

- One container width (`max-w-[1440px]`) and one section rhythm
  (`py-20 / sm:py-24`) site-wide, alternating white and `slate-50` tones.
- **Every CTA routes through the single `<Cta>` component** in `components/ui.tsx`, so
  height, radius, weight and the hover arrow are identical everywhere. Three variants:
  `primary` (gradient blue), `secondary` (white outline), `invert` (white, for the dark footer).
- CTA pairs are always primary-first, equal-height, stacked full-width on mobile and
  centred inline from `sm` up — never mismatched sizes.
- The primary conversion action (**Book a Strategy Call** → Calendly) appears in the nav,
  hero, process, footer band and every page's closing CTA.

## Content fidelity

All copy lives in [`lib/content.ts`](lib/content.ts) as a single typed source of truth,
transcribed verbatim from the live site. Preserved outbound targets:

| Target | Destination |
|---|---|
| Book a Strategy Call | `https://calendly.com/urbanb2bcompany/30min` |
| LinkedIn | `https://www.linkedin.com/company/urbanb2b/` |
| X | `https://x.com/UrbanB2B` |
| Email | `mailto:info@urbanb2b.com` |
| Phone | `tel:+919272106272` |

Internal routes match the original: `/`, `/services`, `/case-studies`, `/about`,
`/contact`. Page titles, meta descriptions, Organization and FAQPage JSON-LD are carried
over, plus generated `sitemap.xml` and `robots.txt`.

## Accessibility & performance

- Skip-to-content link, visible focus rings, `aria-expanded`/`aria-controls` on the mobile
  nav and FAQ, `aria-current` on the active nav item.
- All motion (hero rotator, count-ups, scroll reveals) is disabled under
  `prefers-reduced-motion: reduce`.
- The hero's rotating headline sits in a fixed-height box so nothing below it reflows.
- ~103 kB shared First Load JS; every route prerendered at build time.

## Known gap

**The contact form is presentational.** It validates and shows the success state, but
posts nowhere — matching the source site's behaviour. Wire `handleSubmit` in
[`components/ContactForm.tsx`](components/ContactForm.tsx) to an API route, form service
or CRM endpoint before using it to capture real leads.

## Local development

```bash
npm install
npm run dev     # http://localhost:3000
npm run build   # production build
npm start       # serve the build
```

## Project structure

```
app/
  layout.tsx          root shell, metadata, JSON-LD
  page.tsx            home
  services/           about/  case-studies/  contact/
  globals.css         design tokens, glass, noise, motion
  sitemap.ts  robots.ts  not-found.tsx
components/
  ui.tsx              Cta, SectionHeading, GlassCard, Section, list items
  Navbar  Footer  Hero  PageHero  Background
  ContactForm  Faq  CountUp  Reveal  Icons
lib/
  content.ts          all site copy
```
