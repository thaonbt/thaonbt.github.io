# web-template-astro

Personal Astro blog/knowledge-base template based on [AstroPaper](https://github.com/satnaing/astro-paper) by Sat Naing (MIT).

Live demo (GitHub Pages): [https://thaonbt.github.io/web-template-astro/](https://thaonbt.github.io/web-template-astro/)

The demo on GitHub Pages is only a preview. Production sites built from this template are meant to be deployed on Cloudflare Pages.

## Stack

* [Astro](https://astro.build/) with TypeScript and Tailwind CSS
* Static search with [Pagefind](https://pagefind.app/)
* Markdown and MDX content collections
* Light and dark mode, RSS, sitemap and dynamic OG images

## Requirements

* Node.js 22.12 or later (24 recommended, matching the CI default)
* pnpm (`corepack enable` is enough on recent Node versions)

## Use as a template

1. Click **Use this template** on GitHub to create a new repo (follow the naming convention, for example `kb-thaonbt-blog`).
2. Clone it and install dependencies:
   ```bash
   pnpm install
   ```
3. Edit `astro-paper.config.ts`: `site.url`, `site.title`, `site.description`, `site.author` and social links.
4. Update `base` in `astro.config.ts` to the new repo name (see the next section).
5. Replace the sample posts in `src/content/posts/` and the About page in `src/content/pages/about.md`.

## `site` and `base`

GitHub Pages serves a project repo at `https://<username>.github.io/<repo>/`, so the site root is `/<repo>` instead of `/`.

| Target                            | `site.url`                   | `base`         |
| ----------------------------------- | ---------------------------------- | -------------------- |
| GitHub Pages (project repo)       | `https://thaonbt.github.io/` | `/` |
| Cloudflare Pages or custom domain | your final URL                   | remove it          |

When `base` is set, every internal link must include the prefix. After changing either value, always run a production build and click through the site (see below).

## Commands

| Command            | Action                                                           |
| -------------------- | ------------------------------------------------------------------ |
| `pnpm install` | Install dependencies                                             |
| `pnpm dev`     | Dev server at`http://localhost:4321//`                 |
| `pnpm build`   | Type-check, build to`./dist/`and generate the Pagefind index |
| `pnpm preview` | Serve the production build locally                               |

Search only works after `pnpm build`. In dev mode the search index may be empty.

## Deploy to GitHub Pages (demo)

1. In the repo, open **Settings > Pages** and set **Source** to ​**GitHub Actions**​.
2. Push to `main`. The workflow in `.github/workflows/deploy.yml` builds and publishes the site.

## Deploy to Cloudflare Pages (production)

* Build command: `pnpm build` (so Pagefind indexing runs after the Astro build)
* Output directory: `dist`
* Remove `base` and set `site.url` to the final URL

## Common pitfalls

* **404 on assets or links after deploy:**`base` is missing or a link is hardcoded without the prefix. Test with `pnpm build && pnpm preview`.
* **A post does not show up:** check `draft` and `pubDatetime` in its frontmatter. Drafts and future-dated posts can be skipped in production builds.
* **Two lockfiles:** keep only `pnpm-lock.yaml`. CI picks the package manager from the lockfile.

## Credits and license

Based on [AstroPaper](https://github.com/satnaing/astro-paper) by [Sat Naing](https://satnaing.dev/).

Released under the [MIT License](https://claude.ai/chat/LICENSE). The original copyright notice is preserved as required.

