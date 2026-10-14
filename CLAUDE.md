# CLAUDE.md

Guidance for Claude Code (and humans) working on this repository.

## Purpose

This is the public website of [[LEGAL_NAME]]. Its main job is to show potential customers that the business is real and trustworthy: who we are, what we do, and how to reach us. Clarity and credibility matter more than visual effects.

## Tech rules (decided, do not change without asking)

- **Plain static HTML and CSS.** No framework (no React, Vue, Astro, Tailwind, etc.), no npm, no build step. Every file in the repo is served as-is.
- **JavaScript only where genuinely needed**, as small vanilla JS files in `assets/js/`. The site currently uses none.
- **Hosting:** Cloudflare Pages, connected to the private GitHub repo. Build command: none. Output directory: repo root (`/`).
- **Server-side logic** (only if ever needed, e.g. a contact form handler) goes in `functions/` as a Cloudflare Pages Function. Do not add other backends.
- No third-party trackers, analytics or cookie banners unless the owners ask. If analytics is requested, prefer Cloudflare Web Analytics (cookie-free).
- Keep it fast: no web fonts unless the brand needs one, compress images, set `width`/`height` on `<img>`.

## File map

| Path | What it is |
| --- | --- |
| `index.html` | Home page: hero, services, about, contact |
| `privacy.html` | Privacy policy (legally sensitive, see below) |
| `404.html` | Not-found page (Cloudflare Pages serves it automatically) |
| `assets/css/styles.css` | All styles. Brand tokens are CSS variables at the top in `:root` |
| `assets/img/` | Images, including `og-image.png` (1200×630 social preview) |
| `favicon.svg` | Favicon / logo mark |
| `_headers` | Cloudflare security and caching headers |
| `robots.txt`, `sitemap.xml` | Search engine files. Add new pages to `sitemap.xml` |

Header and footer are repeated in each HTML page (no templating). When you change one, change it on every page.

## Open decisions

- [ ] **Final company name.** "Xordx" is a working name only (2026-10-14); the owners haven't decided. When it changes, replace it everywhere:
  `grep -rl "Xordx" --exclude-dir=.git . | xargs sed -i 's/Xordx/NewName/g'`
  then update the favicon letter in `favicon.svg`, the `og-image.png`, and the GitHub/Cloudflare project names if they used it.
- [ ] Domain: not bought yet. All `[[DOMAIN]]` placeholders stay until it is (likely chosen after the final name).

## Brand guidelines

**Colors** (defined once in `assets/css/styles.css` `:root`; change them there):

| Token | Hex | Use |
| --- | --- | --- |
| `--color-primary` | `#0f2a44` | Deep navy: headings, hero, footer |
| `--color-accent` | `#0e7c7b` | Teal: buttons, links |
| `--color-accent-dark` | `#0a5f5e` | Hover state |
| `--color-text` | `#1f2933` | Body text |
| `--color-muted` | `#52606d` | Secondary text |
| `--color-bg-alt` | `#f4f6f8` | Alternating section background |

Keep text contrast at WCAG AA (4.5:1) or better.

**Fonts:** the system font stack (`--font-sans`). It's fast, needs no external requests, and renders Hebrew well. If a brand font is adopted later, self-host it in `assets/fonts/`.

**Tone of voice:** clear, confident, plain-spoken. Short sentences. Say concretely what we do. No hype ("world-class", "revolutionary"), no jargon, no exclamation marks.

**Logo:** Text wordmark: the company name in bold `--color-primary`. The favicon is the company initial on navy (decided 2026-10-14). `favicon.svg` is currently a placeholder mark.

## Do not change without asking the owners

These are legal or factual details. Never edit, invent, or "improve" them on your own:

- Legal company name: **[[LEGAL_NAME]]**
- Company number (ח.פ.): **[[COMPANY_NUMBER]]**
- Address, phone and email: **[[ADDRESS]]**, **[[PHONE_DISPLAY]]**, **[[EMAIL]]**
- `privacy.html` content (Israeli privacy law applies; changes need review)
- The domain in canonical/OG URLs and `sitemap.xml`

Also never invent facts: no made-up clients, numbers, testimonials, awards, certifications or years of experience. If content is missing, leave a `[[PLACEHOLDER]]` and ask.

The company number must stay visible in both the contact section and the footer of every page.

## Language and RTL

The site is **English only** (decided 2026-10-14). If Hebrew is ever added, put it at `/he/` with `lang="he" dir="rtl"` on `<html>`.

The CSS uses logical properties (`margin-inline`, `padding-block`, `inset-inline-start`, etc.) so it works in both directions. Keep it that way: never use `left`/`right`, `margin-left`, `padding-right` or `text-align: left`. Wrap phone numbers, company numbers and emails inside Hebrew text in `dir="ltr"` so they don't get scrambled.

## Workflow

1. Create a branch: `git checkout -b short-description`
2. Make the change and preview locally: `python3 -m http.server 8000`, then open http://localhost:8000
3. Commit and push the branch: `git push -u origin short-description`
4. Cloudflare builds a preview at `https://short-description.<project>.pages.dev`. Check it on desktop **and** mobile.
5. Open a pull request on GitHub and merge to `main` when it looks right. **Merging to `main` publishes the live site.**

Never commit directly to `main`.

## Before every merge, check

- Every page still shows the legal name, company number and privacy link in the footer
- No `[[PLACEHOLDER]]` text left: `grep -rn "\[\[" --include=*.html .`
- Page looks right at phone width (~375px)
- Links work and images have `alt` text
- `<title>`, `meta description` and Open Graph tags are set on any new page
