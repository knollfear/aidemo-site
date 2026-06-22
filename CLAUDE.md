# Project context: aidemo-site (managed Hugo site)

## What this is
A **Hugo static site** that is the demo/test target for an email-to-website agent. It's the
shakedown site for the pipeline (`aidemo.knollfear.com`) *before* the same pattern is turned
over to the kids' real sites (rileyknoll.com, caseyknoll.com). One site repo = one kid (or
the demo); the **public** site repos hold content only, no secrets.

## The two-repo split (important)
- **This repo (public):** the website — Hugo `content/`, `layouts/`, `static/`. Deployed to
  GitHub Pages. No PII, no API keys.
- **The agent repo (private): `knollfear/ai-site-builder`** — the central agent that manages
  this and other site repos. It takes a natural-language message, calls the Claude API,
  generates markdown, and **opens a PR here touching only `content/`**. Allowed-sender lists,
  `sites.toml`, and the API key live there, never here.

## How content changes
- **Do not hand-author `content/**` as the normal path** — content arrives via agent PRs that
  a human reviews and merges (the review step = parental moderation). Editing content directly
  is fine for fixing the demo, but the real interface is the agent.
- The agent writes **markdown only, never HTML** — a deliberately narrow output schema is what
  keeps a cheap model reliable. Keep that invariant if you touch the agent.
- Sections live as `content/<section>/_index.md`; updates are dated posts under
  `content/updates/`. Nav is auto-generated from sections (see `layouts/partials/nav.html`).

## Layout / styling
- Hand-maintained here: `layouts/_default/{baseof,list,single}.html`, `layouts/index.html`,
  `layouts/partials/nav.html`, `static/css/style.css`. No theme, no SCSS pipeline — plain CSS.
- Hugo **0.120.4 extended** (matches CI). Local preview: `hugo server -D`.

## Deploy
- `.github/workflows/deploy.yml`: push/merge to `main` → Hugo build (`--gc --minify`) →
  GitHub Pages. `baseURL` is injected at build time from the Pages URL, so `hugo.toml`'s
  `baseURL` is not load-bearing for production.
- Custom domain (`aidemo.knollfear.com`) is wired via Cloudflare DNS + Pages custom domain —
  set up separately; confirm the exact hostname before pointing DNS.

## Safety model (why it's built this way)
- The agent can only touch `content/` (enforced in the agent's CI, graduating to a
  branch-protection path filter). Safety is CI config, not model judgment.
- PR-review-before-merge is the moderation gate; auto-merge is the per-site graduation lever.
