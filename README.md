# GitHub Pages Site Guide (`thaonbt.github.io`)

🌐 **Language:** English · [Tiếng Việt](README.vi.md)

Reference documentation for the `thaonbt.github.io` repo: 
- how the site is built, the options for the home page
- and how to handle 404 errors.

## Quick summary

| Goal | File needed | Needs `_config.yml` | Result |
|---|---|---|---|
| Home page written in Markdown | `index.md` | Recommended (to pick a theme) | Jekyll renders it to HTML with a theme |
| Home page with hand-written HTML/CSS | `index.html` | No | File is served as-is with no theme |
| Home page that redirects to another site | `index.html` (redirect only) | No | Visitors are sent away immediately |
| Custom error page | `404.html` | No | Shown for any URL that does not exist |

Core rule: **GitHub Pages does not read `README.md` to build the site.** It only builds from `index.md` or `index.html`. `README.md` is shown only on the GitHub repo page.

---

## 0. Common prerequisites

1. The repo name must be exactly `thaonbt.github.io` (lowercase and matching the username).
2. The repo must be **Public** (free plan).
3. Go to **Settings → Pages** and choose *Deploy from a branch* → branch `main` → folder `/ (root)`.
4. Every commit to `main` triggers the *pages build and deployment* workflow. Follow it in the **Actions** tab (usually 1-2 minutes).
5. When testing, use an incognito window or hard refresh (`Ctrl+Shift+R`) because of caching.

Reference repo layout:

```
thaonbt.github.io/
├── README.md        # English docs (shown only on the repo page)
├── README.vi.md     # Vietnamese docs (shown only on the repo page)
├── _config.yml      # Jekyll and theme config (only needed with index.md)
├── index.md  or  index.html   # PICK ONE, never keep both
├── about.md         # Extra page (optional)
└── 404.html         # Custom 404 page (optional)
```

---

## 1. Using `index.md` (Markdown + Jekyll theme)

**When to use:** you want to write content quickly in Markdown (CV, intro, simple blog) without hand-writing HTML/CSS.

**How it works:** GitHub Pages runs Jekyll, reads `_config.yml` for the theme and renders `index.md` into `index.html` with the theme applied.

### Step 1: Create `_config.yml`

```yaml
title: Thao Nguyen
description: Senior QA engineer exploring the intersection of quality, data and finance.
theme: jekyll-theme-minimal   # built into GitHub Pages
exclude:
  - README.md                 # stops Jekyll from turning these READMEs into site pages
  - README.vi.md
```

Commonly used built-in themes: `jekyll-theme-minimal`, `jekyll-theme-cayman` and `jekyll-theme-slate`.

### Step 2: Create `index.md`

```markdown
---
title: Thao Nguyen
---

# Hi, I'm Thao

Your Markdown content goes here.
```

Front matter (the block between the two `---` lines) is **required** for Jekyll to process the file. Without it the file may be copied as-is and not rendered.

### Step 3: Commit and verify

Commit to `main`, wait for the workflow to finish and open `https://thaonbt.github.io/`.

### Extra pages

Create another `.md` file with a `permalink`:

```markdown
---
title: About
permalink: /about/
---
```

The page will live at `https://thaonbt.github.io/about/`.

### Pitfalls

- Markdown tables need **a blank line above them** or kramdown may not render them as a table.
- Root `README.md` and `README.vi.md` may be rendered by Jekyll as their own pages unless you add the `exclude` entries above.
- For a long-term blog consider **Minimal Mistakes** via `remote_theme: mmistakes/minimal-mistakes` (needs the `jekyll-remote-theme` plugin). It is more powerful but harder to configure.

---

## 2. Using `index.html` (hand-written HTML)

**When to use:** you want full control over the look and no dependency on a Jekyll theme.

**How it works:** GitHub Pages serves the HTML file directly. If the file has **no front matter** Jekyll copies it verbatim without applying any theme or layout.

### Minimal example

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Thao Nguyen</title>
  <style>
    body { font-family: system-ui, sans-serif; max-width: 720px; margin: 10vh auto; padding: 0 1rem; line-height: 1.6; }
  </style>
</head>
<body>
  <h1>Hi, I'm Thao</h1>
  <p>Senior QA Engineer.</p>
</body>
</html>
```

CSS and JS can live in separate files (for example `/assets/style.css`) and be referenced with an absolute path such as `/assets/style.css`.

### Pitfalls

- **Never keep `index.md` and `index.html` together.** Both produce `/index.html` and will conflict.
- `_config.yml` is not required here. If the repo still has one it does no harm.
- To use Jekyll layouts or includes inside an HTML file you must add front matter (`---` then `---`). The file is then processed by Jekyll.

---

## 3. Using `index.html` as an automatic redirect

**When to use:** you want `thaonbt.github.io` to send visitors straight to another site (here `https://profile.thaonbt.workers.dev/`).

**How it works:** GitHub Pages does not support server-side redirects (301). The practical approach is to serve an HTML page that only contains a client-side redirect.

### Create `index.html` (no front matter)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Redirecting…</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <!-- Primary redirect: works even with JavaScript disabled -->
  <meta http-equiv="refresh" content="0; url=https://profile.thaonbt.workers.dev/">
  <!-- Tells search engines where the real page is -->
  <link rel="canonical" href="https://profile.thaonbt.workers.dev/">
  <!-- Fallback: leaves no history entry so the Back button does not get stuck -->
  <script>window.location.replace("https://profile.thaonbt.workers.dev/");</script>
</head>
<body>
  <p>Redirecting… <a href="https://profile.thaonbt.workers.dev/">Click here</a> if your browser does not redirect automatically.</p>
</body>
</html>
```

### Keeping the old CV page (optional)

Rename `index.md` to `about.md` and add `permalink: /about/` to its front matter. The page will live at `/about/`.

### Alternatives

| Option | Pros | Cons |
|---|---|---|
| Meta refresh + JS (above) | No plugin needed and stable | Not a true 301 |
| `jekyll-redirect-from` (`redirect_to:` in the front matter of a `.md` file) | Less code | Still a client-side redirect and depends on the Jekyll build |
| Custom domain + Cloudflare Redirect Rules | True 301 and better for SEO | Needs your own domain and does not apply to `*.github.io` |

### Pitfalls

- Never keep `index.md` and `index.html` together.
- **Redirect loops:** if the destination links or redirects back to `thaonbt.github.io` the browser will loop forever.
- Browsers may cache redirects so test in an incognito window.

---

## 4. Custom `404.html` page

**When to use:** you want visitors who mistype a URL to see a clear page with navigation links instead of the default GitHub 404.

**How it works:** GitHub Pages automatically serves the root `404.html` for any path that does not exist. The HTTP status stays 404.

### What the default 404 looks like

- **Site deployed but URL wrong:** a white page with the GitHub logo and the text "404 File not found".
- **Site not enabled or repo misnamed:** a message saying "There isn't a GitHub Pages site here".

### Create `404.html` (no front matter)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>404 - Page not found</title>
  <meta name="robots" content="noindex">
  <style>
    body { font-family: system-ui, sans-serif; max-width: 480px; margin: 20vh auto; padding: 0 1rem; text-align: center; color: #222; }
    h1 { font-size: 4rem; margin: 0; }
    a { color: #0b5fff; }
  </style>
</head>
<body>
  <h1>404</h1>
  <p>The page you are looking for does not exist or has moved.</p>
  <p>
    <a href="https://profile.thaonbt.workers.dev/">My profile</a> ·
    <a href="https://notes.thaonbt.workers.dev/">Notes</a> ·
    <a href="https://thaonbt.github.io/about/">About</a>
  </p>
</body>
</html>
```

Test it: open `https://thaonbt.github.io/any-path` in an incognito window.

### Trade-off: error page or auto-redirect

- **Error page (recommended):** keeps the correct 404 status so visitors know they mistyped and broken links stay easy to spot.
- **Auto-redirect to the profile:** convenient but treated as a *soft 404* and hides broken links.
- **Middle ground:** keep the error page and add `<meta http-equiv="refresh" content="5; url=https://profile.thaonbt.workers.dev/">` to redirect after 5 seconds.

### Pitfalls

- Use **absolute paths** for CSS/JS/links because `404.html` is served at the broken URL itself (relative paths break on nested URLs such as `/a/b/c`).
- URLs are case-sensitive: `/About/` returns 404 while `/about/` does not.
- If you write it in Markdown (`404.md`) it needs `permalink: /404.html` or the output becomes `/404/index.html` and GitHub will not recognize it.
- There is only **one** `404.html` at the root with no per-folder 404 pages.

---

## 5. Troubleshooting checklist

1. Is the repo named exactly `thaonbt.github.io` and set to Public?
2. Does **Settings → Pages** point to `main` / `/ (root)`?
3. Does the **Actions** tab show an error in the *pages build and deployment* workflow?
4. Do `index.md` and `index.html` both exist?
5. Does the `.md` file have front matter (`---`)?
6. Did you hard refresh or try an incognito window?
7. Do the file names and URL match in letter case?
