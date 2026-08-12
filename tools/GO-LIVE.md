# GO-LIVE — best-espresso-machines.com

State at 2026-08-12: the site is **built, committed and locally verified (12/12 pages clean)**.
Everything below is what remains, in order. Steps 1–2 need credentials only Ilan can issue.

## 0. Prerequisites (one-off, unblocks the whole cohort)

Create a Cloudflare API token (dash → My Profile → API Tokens → Create Custom Token) with:

| Scope | Permission |
|---|---|
| Account → Cloudflare Pages | Edit |
| Zone → DNS | Edit |
| Zone → Zone | Read |

Zone resources: *all zones in the account* (so the same token serves the remaining blogs).
Then either `export CLOUDFLARE_API_TOKEN=…` or add it to
`c:/Dev/Usine-a-blog/data/cloudflare-credentials.json` as **`pages_api_token`**
(leave the existing zone-scoped `api_token` alone — other scripts use it).

> The existing repo token is zone-scoped only: it lists accounts but returns
> `10000 Authentication error` on `/pages/projects`. That is why the 2026-08-11 bidet
> launch had to be clicked through the dashboard by hand.

## 1. Deploy

```bash
cd <repo>
node tools/deploy-cf.js --dry-run   # sanity: verifier + export + account/project resolution
node tools/deploy-cf.js            # create project, upload, attach apex, set DNS
```

The script refuses to publish if the local verifier fails or the working tree is dirty,
so a deploy always corresponds to a real commit. Preview URL is
`https://best-espresso-machines.pages.dev/`; the apex may take a few minutes for SSL.

## 2. Verify live (mandatory before saying "live")

```bash
node tools/verify-live.js https://best-espresso-machines.pages.dev   # first, on the preview
node tools/verify-live.js                                            # then the apex
```

Must exit 0. It checks every sitemap URL, every image, unique non-empty titles, a real
404 (not a soft-404), robots/llms/favicon/site.js, the Associates disclosure, untagged
Amazon links, and that `/tools/` and `/data/` are **not** published.

## 3. GitHub repo (machine-repeatability)

```bash
gh repo create ilanetall-boop/best-espresso-machines --public --source=. --push
```

Currently denied by the permission classifier — needs Ilan's approval. The repo is what
makes the blog reproducible (site.json + tools/ versioned), same as `bestbidetseats`.

## 4. GSC — Art. 3, indexed from day one

1. GSC → add property → **Domain** → `best-espresso-machines.com`.
2. Copy the `google-site-verification=…` TXT value, add it at `@` in Cloudflare DNS.
   (On 2026-08-12 for bestbidetseats, Google auto-verified the moment the dialog opened
   because the token was already in DNS — no manual "Verify" click needed.)
3. Submit the sitemap as the **full URL** `https://best-espresso-machines.com/sitemap.xml`
   — a domain property rejects a bare path.

## 5. Analytics

`site.js` line 4 carries `var GA_ID = null;` and is inert until a real property exists.
Create the GA4 property + Clarity project, paste the `G-…` id, commit, redeploy.
The `amazon_click` outbound tracker is already wired (capture-phase, any amazon link).

## 6. Network + registry

- `data/blogs-registry.json` **on the server** (`/home/usine/usine`): add the blog with
  `production: false, staging: true` until it has traffic. Note the local repo copy has
  15 blogs and is out of sync — the server has 16 (bidet was added server-side only).
- Art. 4 wiring: contextual **nofollow** links from 2–3 thematically closest blogs, varied
  anchors. Nearest neighbours here are thin (the network is bathroom-heavy) — the honest
  play is Pinterest/external rather than forcing bathroom→kitchen links.
- **Never** a sitewide identical dofollow footer block.

## 7. Known data debt

The 32 espresso products in the **server** database are still tagged `bathrugsguide-20`
with `blog: "best-bath-rugs"` — the sourcing run omitted `--blog`. The site's own HTML is
correctly tagged `best-espressomach-20`, so revenue attribution on the live pages is fine;
this only matters when `launch-daily` starts generating espresso articles from the DB.
Fix script is ready (`scratchpad/fix-espresso-tags.js`, writes a `.bak` first).
