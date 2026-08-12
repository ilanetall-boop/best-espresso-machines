# AGENT BRIEF — writing a new article for this site (premium-editorial-v1 · espresso)

This is the standard every article on Best Espresso Machines must meet. Deviation = the site
slides back to network-average quality. Read `index.html` before you write a line.

## The pipeline for one new article

1. Pick topic + real products (from `tools/PRODUCTS.md` — the verified database extract).
   NEVER invent products, prices or specifications.
2. Hero image: `images/hero-<name>.webp` (1600×900), generated separately and eyeballed
   on a contact sheet before deploy. Reference it; don't create it.
3. Write `<slug>.html` per the template contract below.
4. Add one entry to `data/site.json` → run `node tools/rebuild-site.js`
   (regenerates /guides, homepage block, sitemap, llms.txt AND verifies; exit 1 = do not deploy).
5. Deploy the clean export: `git archive HEAD | tar -x -C ../espresso-live`. Never deploy `.git/`.

## Template contract (what the HTML file must contain)

- Reuse from `index.html`, VERBATIM: the entire `<style>` block, the `<style id="a11y">`
  block, `<header class="site">…</header>` and `<footer class="site">…</footer>`.
  Append only minimal article CSS (prose ~68ch). Never restyle `.buy` / `.btn` —
  the a11y block owns them, and overriding them recreates the invisible-CTA bug.
- Head: unique `<title>` + meta description, `<link rel="canonical" href="https://best-espresso-machines.com/<slug>">`,
  the 5 favicon links, `theme-color #1A100C`, OG tags (`og:type article`).
- JSON-LD: `Article` (author Ilane Tall, datePublished + dateModified 2026-08-12)
  + `FAQPage` with 3–4 real FAQs that also appear in the body.
  Add `HowTo` for how-to guides. For mini-roundups use `ItemList` of `Product` with
  `brand` + `offers` — **never `aggregateRating`** (see the no-ratings rule below).
- Hero `<img src="/images/hero-<name>.webp" width="1600" height="900" loading="eager"
  fetchpriority="high" decoding="async">` immediately under the H1 block.
- Product images: `/images/amazon-<ASIN>.webp`, `width="800" height="800" loading="lazy"`.
- Amazon links: `https://www.amazon.com/dp/<ASIN>?tag=best-espressomach-20` with
  `rel="nofollow sponsored noopener" target="_blank"`. No untagged `/dp/` links, ever.
- Byline: `By Ilane Tall · Updated August 12, 2026 · Research-based.`
- An affiliate-disclosure line near the top; the footer already carries the exact
  "As an Amazon Associate, we earn from qualifying purchases."

## The no-ratings rule (specific to this site)

Amazon's API returns **no rating and no review count** for these 32 machines. Therefore:

- **NEVER** print a star rating, a rating number, a review count, or "4.5★ from 2,300 buyers".
- **NEVER** write "owners report", "reviewers consistently note", "buyers say" or any
  paraphrase of review sentiment. We have no review data. Inventing sentiment is the
  same offence as inventing a score.
- Anchor every claim in: published specs, what's in the box, machine class
  (steam-driven / pump semi-automatic / super-automatic / capsule), footprint, wattage,
  tank size, price — plus general, non-product-specific coffee facts
  (e.g. espresso brews at ~9 bar; burr grinders beat blade grinders).
- Where a trade-off is a judgement, say so in your own voice: "The trade-off is …".
- Say the honest thing once per article: we compare specs and prices, we don't run a
  testing lab, and we don't publish ratings we can't verify.

## Editorial bar

- 2,500–4,000 words of specific, useful, conversational-but-authoritative English for a
  US audience. No fluff, no repetition, no AI-listicle cadence, no emoji, no scores out of 10.
- FORBIDDEN phrases: "we tested", "I tested", "after testing", "in our testing",
  "hands-on", "our testing lab". Use "we compared", "we researched", "the spec sheet shows".
- Any cost/savings math must state its assumptions inline.
- Internal links: to `/` and 3–5 sibling articles **in context**, plus a "Keep reading"
  block at the end. Sibling slugs are listed in `data/site.json`.
- Health/safety-adjacent points (caffeine, descaling chemicals, hot steam) framed as
  general information and manufacturer guidance, never as medical advice.

## Verification gate (non-negotiable)

`node tools/rebuild-site.js` must exit 0. It checks: forbidden claims, untagged or
missing-rel Amazon links, missing images, dead internal links, dead same-page anchors,
disclosure presence, and site.json↔files coherence. A FAIL means fix, not deploy.

After deploy: load the page, confirm the hero renders and is on-topic, click two internal
links, confirm the article appears on /guides and in sitemap.xml.
