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
writing/*.html      Live posts of the new weekly "Rolling Research × Company AI" series, slug-named
styles.css          Single shared stylesheet for the whole site
logo.svg            Brand mark
og-image.html       Source template for the social share image
og-image.png        Rendered OG image (referenced by every page)
robots.txt
sitemap.xml
CNAME               Custom domain (siblingsystems.limited). GitHub-only — never delete or edit
README.md           Repo readme. GitHub-only

drafts-html/        (git-ignored) upcoming posts staged as HTML; git mv into writing/ to publish
drafts-source/      (git-ignored) Ian's raw markdown drafts for the new series
archive/            (git-ignored) the retired first 8-post series, kept as source to rework
```

There is no local `case-study.html`. The Thumbtack case study now lives on Medium; every "Case Study" link points to that external URL (`target="_blank" rel="noopener"`). Don't re-add a local case-study page or relink to one.

## Conventions (match these exactly)

**Every page** links `styles.css` (root pages) or `../styles.css` (pages in `writing/`), preloads Google Fonts (Fraunces + IBM Plex Sans), and includes the same `<head>` block: `charset`, `viewport`, favicon `/logo.svg`, a `canonical` URL, meta description, full OpenGraph + Twitter card tags, and JSON-LD. When adding a page, copy an existing one's `<head>` and change only the page-specific values.

**Design tokens** live in `:root` in `styles.css` — `--bg #F5F3EE`, `--ink #1C1C1A`, `--muted`, `--rule`, `--accent #2A4038`, `--serif Fraunces`, `--sans IBM Plex Sans`. Use these variables; never hard-code colors or font stacks. Body weight is 300; headings use the serif. Reuse existing component classes (`.section-label`, `.steps`/`.step`/`.step-num`, `.proof-numbers`/`.proof-stat`, `.services-grid`/`.service`, `.btn-primary`, etc. — see the class list in `styles.css`) rather than writing new CSS. Occasional one-off inline `style=""` is used for page-specific tweaks; keep it minimal.

**Nav + footer** are duplicated verbatim in every page (no includes/templating). If you change one, change all of them. Nav links: Writing, Case Study (external Medium link), "Get in touch" (mailto). Contact everywhere is `mailto:ian@siblingsystems.limited`.

**JSON-LD** is centralized: `index.html` holds the `@graph` defining `#organization`, `#ian-hall`, and the five `Service` nodes. Other pages reference those by `@id` (e.g. `"provider": { "@id": ".../#organization" }`). Posts use `BlogPosting`; `audit.html` uses `Service`. Keep `@id`s stable.

## The blog — new weekly series

The original 8-post series was **retired**; its files sit in `archive/` (git-ignored) as raw material to rework later. Don't ship, relink, or resurrect them. The live blog is a **new weekly series**, published one post at a time. Ian's source drafts live in `drafts-source/2026-new-series-drafts.md` (git-ignored).

### Series roadmap

| # | `writing/` slug | Title | Thesis | Status |
|---|---|---|---|---|
| 1 | `ai-convergence-commoditized-customer-contact.html` | AI convergence has commoditized everything except customer contact | Models converge on the same public data; contact with real customers is the last moat | **Live — 2026-06-20** |
| 2 | `research-is-slow-is-a-design-flaw.html` | "Research is slow" is a design flaw, not a law | Speed is a logistics problem; the Rolling Research machine step by step (intake → Friday triage → 2-wk turnaround → Flash Findings) | Draft |
| 3 | `alignment-is-an-information-problem.html` | Alignment is an information problem | A shared, validated corpus of Flash Findings is what alignment mechanically is — for humans and AI | Draft |
| 4 | `synthetic-users-belong-in-your-pilot.html` | Synthetic users belong in your pilot, not your evidence | Synthetic = instrument; human = evidence. Never let synthetic responses into the corpus | Draft |
| 5 | `i-train-my-replacement.html` | I train my replacement | The 90-day install model: build the machine, train the owner, then leave | Draft |

Cadence: **one per week**, starting 2026-06-20. Dates past post 1 are provisional — confirm each with Ian before building. (Slugs for posts 2–5 are proposed here; keep them stable once a post ships.)

### Voice rule (non-negotiable)

**Posts are Ian's verbatim words.** Convert drafts faithfully — never rewrite, expand, tighten, reorder, or add sentences. You MAY add: section headers (with Ian's OK), inline citation links, and the house `<head>` (meta/OG/JSON-LD). You may NOT add CTA copy, pull-quotes that re-punctuate his sentences, or any editorializing. Coach, not player. Set the meta/OG/JSON-LD description to Ian's own opening line so even the SEO text is his.

### Internal cross-references

The drafts reference each other as "Post 1"…"Post 5" (e.g. "the collapse loop from Post 1", "the machine from Post 2", "the corpus from Post 3"). When building a post, convert those into inline links to the referenced post's slug — but only to posts already built/live; leave forward references as plain text until that post ships.

### Date-gate + publishing mechanics

`writing/index.html` lists each post as a `.post-item` with `data-publish="YYYY-MM-DD"`. An inline script dims any post whose date is still in the future, strips its `href`, and relabels the meta to "Coming <date>", then reveals it automatically on the day. Don't "unhide" by editing the index — change `data-publish`.

Only live posts sit in `writing/`. Build upcoming posts in `drafts-html/` (git-ignored); `git mv drafts-html/<slug>.html writing/<slug>.html` a day or two before the date. Never `git add -f` a draft. Slugs only — never `post-N.html`.

When you add or reschedule a post, keep these three in sync:
1. `data-publish` in `writing/index.html`
2. `<lastmod>` for that URL in `sitemap.xml`
3. `datePublished` / `article:published_time` in the post's `<head>`

Verified citations for this series: Shumailov et al., *Nature* (2024) — https://www.nature.com/articles/s41586-024-07566-y ; Gerstgrasser et al. (2024), arXiv:2404.01413 — https://arxiv.org/abs/2404.01413

## Canonical facts (identical everywhere — site, posts, JSON-LD)

- Thumbtack Rolling Research: **21 studies, 15 product teams, 94% CSAT, +75% research volume**, 2-week standard turnaround, over 9 months.
- The pilot Ian scaled into the program was run by the agency **MeasuringU** — name it, link `https://measuringu.com/`.
- Career: **10+ years, 85+ projects, 900+ moderated sessions**; teams at Figma, TripAdvisor, Rocket Mortgage, lululemon, Vimeo, Meta × CZI.
- **CSAT is 94%** everywhere. (A draft of post 1 said 95%; 94% is canonical — corrected on publish.)

## Other notes

- **OG image:** `og-image.html` (1200×630) is the design source; `og-image.png` is the rendered output every page points to. To change the share image, edit the HTML and re-render to PNG.
- **Playbook waitlist** (`index.html`) posts to the live Formspree form `formspree.io/f/mnjyovpz`.
- **Pricing anchors** (keep consistent across pages + JSON-LD): Audit $7,500 fixed / 2 weeks; Playbook $500–1,500 per seat, $7,500 private team; Sprint, Retainer, Fractional = "contact for pricing."
- The site is `© 2026`, "Denver, CO", remote-first.

## Local preview

No build. Open files directly, or serve the folder: `python3 -m http.server` from the repo root, then visit `http://localhost:8000`. Use a server (not `file://`) so root-absolute paths like `/logo.svg` and `/audit.html` resolve.

## Editing principles

Keep diffs surgical and match the surrounding hand-written style. Preserve the meta/JSON-LD discipline — it's load-bearing for SEO and is the most error-prone thing to get wrong. When copy changes touch a stat or price, grep the whole site for the old value so every page and the structured data stay consistent.
