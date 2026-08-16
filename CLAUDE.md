# CLAUDE.md

Guidance for Claude Code working in this repository.

## What this is

Marketing site for **Sibling Systems** (siblingsystems.limited) — a solo embedded-UX-research consultancy (Ian Hall). Positioning: "Rolling Research" keeps a company's AI accurate by keeping user data fresh.

**Stack: hand-written static HTML + one shared CSS file. No framework, no build step, no package manager, no bundler.** Files are deployed as-is. Do not introduce a build pipeline, npm, or a framework unless explicitly asked.

The repo is `inhll/inhll`, deployed to siblingsystems.limited via **GitHub Pages** (custom domain pinned by the `CNAME` file). Work on a branch and open a PR; Pages redeploys on merge to `main`. The site is static — what's in the repo is exactly what ships.

## Layout

```
index.html          Homepage (hero, services ladder, proof, testimonials, about)
audit.html          AI Ground Truth Audit service page
writing/index.html  Blog index ("Field Notes"), date-gated post list
writing/*.html      Individual posts (the "Rolling Research × Company AI" series), slug-named
styles.css          Single shared stylesheet for the whole site
logo.svg            Brand mark
og-image.html       Source template for the social share image
og-image.png        Rendered OG image (referenced by every page)
robots.txt
sitemap.xml
CNAME               Custom domain (siblingsystems.limited). GitHub-only — never delete or edit
README.md           Repo readme. GitHub-only
```

There is no local `case-study.html`. The Thumbtack case study now lives on Medium; every "Case Study" link points to that external URL (`target="_blank" rel="noopener"`). Don't re-add a local case-study page or relink to one.

## Conventions (match these exactly)

**Every page** links `styles.css` (root pages) or `../styles.css` (pages in `writing/`), preloads Google Fonts (Fraunces + IBM Plex Sans), and includes the same `<head>` block: `charset`, `viewport`, favicon `/logo.svg`, a `canonical` URL, meta description, full OpenGraph + Twitter card tags, and JSON-LD. When adding a page, copy an existing one's `<head>` and change only the page-specific values.

**Design tokens** live in `:root` in `styles.css` — `--bg #F5F3EE`, `--ink #1C1C1A`, `--muted`, `--rule`, `--accent #2A4038`, `--serif Fraunces`, `--sans IBM Plex Sans`. Use these variables; never hard-code colors or font stacks. Body weight is 300; headings use the serif. Reuse existing component classes (`.section-label`, `.steps`/`.step`/`.step-num`, `.proof-numbers`/`.proof-stat`, `.services-grid`/`.service`, `.btn-primary`, etc. — see the class list in `styles.css`) rather than writing new CSS. Occasional one-off inline `style=""` is used for page-specific tweaks; keep it minimal.

**Nav + footer** are duplicated verbatim in every page (no includes/templating). If you change one, change all of them. Nav links: Writing, Case Study (external Medium link), "Get in touch" (mailto). Contact everywhere is `mailto:ian@siblingsystems.limited`.

**JSON-LD** is centralized: `index.html` holds the `@graph` defining `#organization`, `#ian-hall`, and the five `Service` nodes. Other pages reference those by `@id` (e.g. `"provider": { "@id": ".../#organization" }`). Posts use `BlogPosting`; `audit.html` uses `Service`. Keep `@id`s stable.

## Date-gated blog (important gotcha)

`writing/index.html` **lists all 8 posts** but gates them with `data-publish="YYYY-MM-DD"`. An inline script at the bottom dims any post whose date is still in the future, removes its `href`, and relabels the meta to "Coming <date>". Once a post's file is in `writing/`, it reveals itself automatically on its date — **no manual editing of the index needed to publish.** Don't "unhide" a post by editing the index; change its `data-publish` date if the schedule changes.

**Publishing model (important):** only the live post(s) sit in `writing/`. Unpublished posts are held in `drafts-html/`, which is **git-ignored** so they never ship by accident — edit them there freely. To publish one, `git mv drafts-html/<slug>.html writing/<slug>.html` (a day or two before its `data-publish` date so the gated link is never dead when it opens), on a branch → PR → merge. Never `git add -f` a draft, and keep filenames as slugs (`your-moat-is-what-your-ai-eats.html`), never `post-N.html` — the sitemap, index links, and SEO all point at the slugs. Each post links prev/next via `.post-nav`.

When you add or reschedule a post, keep these three in sync:
1. `data-publish` in `writing/index.html`
2. `<lastmod>` for that URL in `sitemap.xml`
3. `datePublished` / `article:published_time` in the post's `<head>`

## Other notes

- **OG image:** `og-image.html` (1200×630) is the design source; `og-image.png` is the rendered output every page points to. To change the share image, edit the HTML and re-render to PNG.
- **Playbook waitlist** (`index.html`) posts to the live Formspree form `formspree.io/f/mnjyovpz`.
- **Pricing anchors** (keep consistent across pages + JSON-LD): Audit $7,500 fixed / 2 weeks; Playbook $500–1,500 per seat, $7,500 private team; Sprint, Retainer, Fractional = "contact for pricing."
- The site is `© 2026`, "Denver, CO", remote-first.

## Local preview

No build. Open files directly, or serve the folder: `python3 -m http.server` from the repo root, then visit `http://localhost:8000`. Use a server (not `file://`) so root-absolute paths like `/logo.svg` and `/audit.html` resolve.

## Editing principles

Keep diffs surgical and match the surrounding hand-written style. Preserve the meta/JSON-LD discipline — it's load-bearing for SEO and is the most error-prone thing to get wrong. When copy changes touch a stat or price, grep the whole site for the old value so every page and the structured data stay consistent.
