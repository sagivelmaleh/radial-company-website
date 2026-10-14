# CLAUDE.md

Guidance for Claude Code (and humans) working on this repository.

## Purpose

This is the public website of VEERO (legal entity: [[LEGAL_NAME]]), a data company. Its job is to show potential customers that the business is real and trustworthy and to make the next step obvious: understand the offer, find a relevant service, understand the method, start a conversation.

**The design and copy follow `docs/VEERO_Brand_Book_Website_Specification.pdf` (Edition 1.0).** Read it before changing design or copy. Its colors, font, wordmark and all page copy are *proposed* values pending owner confirmation.

## Tech rules (decided, do not change without asking)

- **Plain static HTML and CSS.** No framework, no npm, no build step. Every file is served as-is.
- **JavaScript only where genuinely needed.** Currently one file: `assets/js/menu.js` (the mobile Menu button). The site works without it.
- **Hosting:** Cloudflare Pages via **Direct Upload** (zip or folder drag-and-drop). The GitHub repo is the master copy. When uploading, include only the public files (not `docs/`, `README.md`, `CLAUDE.md`).
- **Server-side logic** (only if ever needed, e.g. a contact form) goes in `functions/` as a Cloudflare Pages Function.
- No third-party trackers, analytics or cookie banners unless the owners ask.
- Fonts are self-hosted (`assets/fonts/`). Do not load Google Fonts or other CDNs.

## File map

Clean URLs: each page is a folder with an `index.html` (e.g. `/services/data-strategy/`).

| Path | What it is |
| --- | --- |
| `index.html` | Home: hero, value blocks, services, method, closing CTA |
| `services/index.html` | Services hub (4 cards) |
| `services/{data-strategy,data-engineering,analytics,applied-ai}/` | Service detail pages |
| `approach/`, `about/`, `contact/` | Core pages |
| `privacy/`, `accessibility/` | Legal/utility pages (drafts, see below) |
| `404.html` | Not-found page (Cloudflare serves it with HTTP 404) |
| `assets/css/styles.css` | All styles. Brand tokens are CSS variables in `:root` |
| `assets/js/menu.js` | Mobile menu toggle |
| `assets/fonts/` | Inter variable font (WOFF2) + its OFL license |
| `assets/img/data-field-dark.svg` | Decorative "Data as Art" field used in the home hero |
| `assets/img/og-image.png` | 1200×630 social preview |
| `favicon.svg` | Provisional favicon (lime dot, not a monogram) |
| `_headers`, `_redirects` | Cloudflare headers and redirects |
| `robots.txt`, `sitemap.xml` | Search engine files. Add new pages to `sitemap.xml` |
| `docs/` | Brand book PDF. Not part of the public site |

The header and footer are repeated in every HTML file (no templating). When you change one, change it on every page: `grep -rl 'class="site-header' --include=*.html .`

## Brand rules (summary of the brand book)

**Colors** (`assets/css/styles.css` `:root`):

| Token | Hex | Use |
| --- | --- | --- |
| `--veero-dark` | `#111A16` | Forest black: hero, header, footer, closing CTA |
| `--veero-ink` | `#17211C` | Body text, headings |
| `--veero-lime` | `#C7F36B` | Signal lime: the word "impact" on dark, primary buttons. **Never as text on light backgrounds** |
| `--veero-paper` | `#F7F8F4` | Page background |
| `--veero-soft` | `#F3F9E9` | Quiet tinted surface |
| `--veero-muted` | `#59665D` | Secondary text, control borders |
| `--veero-green` | `#456326` | Links on light; "impact" in light headlines |
| `--veero-line` | `#D7DED3` | Hairlines, card borders |

Roughly 75-85% neutral surfaces, 10-20% dark areas, at most 5% lime. The wordmark and nav stay neutral; lime is for emphasis and actions.

**Type:** Inter only, weights 400-600 (700 for the wordmark). Sentence case. Uppercase only for short eyebrows (0.08em tracking).

**Wordmark:** "VEERO" typeset in Inter Bold until the official SVG logo is supplied. Don't invent a symbol or monogram.

**Art direction ("Data as Art"):** only dots, circles and squares, quiet and low-opacity, with large empty areas. **Never:** architecture, buildings, glass panels, glassmorphism, waves, ribbons, blobs, neon, "AI brain" imagery, or decorative charts that look like real data. Motion: none by default.

**Lime wash:** the subtle radial gradient `--wash`, used only on page intros. Max one per viewport, never behind long text, cards or buttons.

**Voice:** precise, calm, capable. Direct verbs (define, connect, build, explain, improve). Lead with the business question. "We" for our work, "you" for the client. Avoid hype ("unparalleled synergy", "guaranteed ROI").

## Do not change without asking the owners

- Legal company name **[[LEGAL_NAME]]**, company number (ח.פ.) **[[COMPANY_NUMBER]]**, address, phone and email
- Privacy and accessibility page content
- The service list and scope. The four services are the brand book's *assumption*, not yet confirmed by the owners
- The domain in canonical/OG URLs and `sitemap.xml`

**Never invent facts:** no metrics, customer names, logos, testimonials, certifications, team bios, founding dates or case-study results. The brand book's publication rule: hide unsupported proof modules rather than filling them. That is why there are no Work or Insights pages and no team section yet.

## Open decisions

- [ ] **Confirm the brand:** palette, Inter, and the provisional wordmark (official logo SVG + favicon still to come).
- [ ] **Confirm services and all copy.** Note: earlier the owners described the business as "collecting alternative data for businesses"; the brand book describes data strategy, engineering, analytics and applied AI. Reconcile before launch.
- [ ] **Legal and contact details:** legal name, company number, address, phone, email (all still `[[PLACEHOLDERS]]`).
- [ ] **Contact form:** owners chose no form for now; `/contact/` shows email and phone. The brand book specifies a form (fields and copy on p.15 and p.30) if one is added later, via a Pages Function.
- [ ] **Work and Insights pages:** add only when real, approved case studies or articles exist (brand book p.27-29).
- [ ] **Launch switch:** pages carry `noindex` and `robots.txt` has `Disallow: /`. Remove both when the content is approved.
- [ ] **Domain:** not bought yet.

## Workflow

1. Create a branch: `git checkout -b short-description`
2. Preview locally: `python3 -m http.server 8000`, then open http://localhost:8000
3. Commit, push the branch, open a pull request, merge to `main`.
4. To publish: in Cloudflare, go to the Pages project, choose **Create deployment**, and upload the public files (or the zip Claude prepares).

## Before every merge, check

- Header and footer are identical on all pages; footer shows the legal name and company number
- No unintended `[[PLACEHOLDER]]`: `grep -rn "\[\[" --include=*.html .`
- Page works at 320 px and 375 px wide with no horizontal scroll, and the Menu button opens and closes (Escape too)
- Lime is never used for text on a light background
- Every new page has a unique `<title>`, meta description and Open Graph tags, and is listed in `sitemap.xml`
