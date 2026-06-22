# aidemo-site

A Hugo static site, **managed by the [ai-site-builder](https://github.com/knollfear/ai-site-builder) agent**.
This is the demo/test site for the email-to-website pipeline before it's turned over to the kids' real sites.

- **Content** (`content/**`) is written by the agent via pull request. Review + merge to publish.
- **Layouts / styling** (`layouts/`, `static/`) are hand-maintained here.
- **Deploy:** merging to `main` builds with Hugo and deploys to GitHub Pages (`.github/workflows/deploy.yml`).

## Local preview

```sh
hugo server -D
```

## How updates happen

The central agent (separate private repo) takes a natural-language message, generates a
markdown update, and opens a PR here touching only `content/`. Merging the PR deploys.
