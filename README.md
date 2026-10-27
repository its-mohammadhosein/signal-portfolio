# Signal (legacy static export)

This repo is a **static HTML crawl** of a WordPress site built on the Salient theme's
"Signal" demo (an agency/portfolio one-pager plus a small work/case-study section). It is
not a hand-built app — it's a scrape of the live WordPress install (theme assets, WPBakery
page-builder markup, FluentForm, uploads, and WP infrastructure files like `wp-json/*` and
`feed/`), kept here as the **source of record** for migrating this site's content and
design into the new app at `../signal-portfolio-payload` (Payload CMS + Next.js).

**No migration has been performed yet.** This README only documents what exists here and
where the extracted migration docs live. The actual migration work happens later, in
`signal-portfolio-payload`.

## What's actually in this crawl

Real content pages (everything else is WordPress plumbing — see below):

- `index.html` — Home. A one-page site: hero, intro statement, services, a "noise"
  statement band, featured work grid, video showreel, testimonials, contact form, footer.
- `work/index.html` — Work archive: a filterable grid of 5 portfolio project cards.
- `portfolio/crystal-vibes/index.html`, `portfolio/radiant/index.html` — the only 2 of
  those 5 projects that were crawled as full case-study pages (Into the Heat, Framework,
  and Balanced exist only as grid cards — see the gap noted in `CONTENT.md`).

Not real content (WordPress/theme infrastructure, safe to ignore during migration):

- `feed/index.html`, `comments/feed/index.html` — RSS feeds
- `wp-json/**` — WP REST API responses
- `index__p=85.html`, `index__p=89.html`, `index__p=485.html` — duplicate `?page_id=`
  preview renders of pages already listed above (Radiant, Crystal Vibes, Work)
- `wp-content/themes/salient/**`, `wp-content/plugins/**`, `wp-includes/**` — theme/plugin
  code and compiled CSS/JS (useful as a *design reference*, not content — see below)
- `wp-content/uploads/**` — the real media library (images/video actually used by the
  pages above, plus many WP auto-generated resized duplicates of each image)

## Where the migration docs are

Full extraction of this site's content, media, and design system — everything a future
Claude Code session needs to perform the actual migration — lives in the target repo:

- **`../signal-portfolio-payload/migration/CONTENT.md`** — literal, page-by-page content
  inventory: nav, footer, every section's verbatim copy, the contact form's fields, the
  work grid, and both case studies. Also documents known gaps (3 missing case-study
  pages, a brand-name inconsistency in the source: header says "Salient", everything else
  says "Signal").
- **`../signal-portfolio-payload/migration/ASSETS.md`** — a deduplicated table of every
  original image/video actually used, which page(s) use it, and what's missing locally
  (6 services-section images are hot-linked to the live demo site, not present in this
  crawl).
- **`../signal-portfolio-payload/migration/DESIGN.md`** — the design system extracted from
  Salient's compiled theme CSS: fonts (Zalando Sans + Source Serif Pro), color palette
  (ink `#121213` / paper `#f1efed` / accent `#ff6022`), type scale, button/card/motion
  patterns.

Those three files plus this README are the complete input set for the migration — no
further crawling of this repo should be needed.

## Migration target, at a glance

The destination app (`signal-portfolio-payload`) is the official Payload CMS website
template (Next.js App Router). It currently ships with:

- Collections: `Pages`, `Posts`, `Media`, `Categories`, `Users` — **no dedicated
  portfolio/project collection exists yet**. A migration will need to decide whether
  work/case-study items become their own collection (mirroring `Pages`/`Posts`, with
  gallery + client-meta fields) or reuse `Posts` + a "Work" category.
- Layout blocks already available: Hero (`HighImpact`/`MediumImpact`/`LowImpact`),
  `Content`, `MediaBlock`, `CallToAction`, `ArchiveBlock`, `Form`, `RelatedPosts`.
  The home page's sections map roughly onto these (hero → Hero, services/testimonials →
  new blocks or `Content`, featured work → `ArchiveBlock`, contact → `Form`), but
  **testimonials and the video showreel band have no existing block** — new blocks would
  be needed.
- The `Form` block's field set (`Text`, `Textarea`, `Email`, `Number`, `Select`,
  `Checkbox`, `Country`, `State`) doesn't have a multi-checkbox group out of the box — the
  source contact form's 4-option "services interested in" checkbox group will need either
  4 individual `Checkbox` fields or a multi-select `Select` field.

This section is a heads-up for planning, not a decision — the actual data-modeling choices
belong to the migration work itself, done in `signal-portfolio-payload`.
