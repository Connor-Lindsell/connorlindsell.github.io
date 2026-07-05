# Connor Lindsell — Engineering Portfolio

Source for [connor-lindsell.github.io](https://connor-lindsell.github.io), a Jekyll-based portfolio site for Connor Lindsell (mechanical and mechatronic engineering — robotics, mechanical design, and applied manufacturing).

## Overview

- **Framework:** [Jekyll 3.10](https://jekyllrb.com/) via the `github-pages` gem, so the build matches what GitHub Pages runs in production.
- **Hosting / deploy:** GitHub Pages, built and deployed by GitHub Actions on every push to `main` (see [Deployment](#deployment)).
- **Design:** a custom, hand-built design system — a dark, washed-grey/blue-accent, technical-minimalist theme (self-hosted Inter + JetBrains Mono, datasheet-style project pages, CSS-only scroll reveals). There is no `colors:` theming config; the look lives entirely in `css/styles.css`.
- **Content model:** each project is a page in the `_projects` collection (`/projects/<slug>/`), rendered through `_layouts/post.html`, with an about page (`about.md`) driven by an experience-timeline data file.

This repo started from the [Free-To-Engineer](https://github.com/leea12/freeToEngineer) portfolio template (© [lowinertia.com](https://lowinertia.com), CC BY 4.0) and has since been fully redesigned — see [Credits](#credits).

## Local development

Prerequisites: Ruby (with Bundler) — no Node/JS build step is required.

```sh
bundle install
bundle exec jekyll serve
```

Then open <http://localhost:4000>. Jekyll watches the source and rebuilds on save; restart the server if you edit `_config.yml`.

## Site configuration (`_config.yml`)

| Key | Purpose |
|---|---|
| `title` | Browser tab title. |
| `tagline` | Short line used by the SEO tag (`{% seo %}`) instead of the full `description`. |
| `name` | Displayed as your name in the hero. |
| `headline` | One-line role/title shown under your name. |
| `description` | Longer hero paragraph (multi-line block scalar — keep the `>-`). |
| `profile_image` | Path to the profile photo used on `/about/`. |
| `resume-url` | External link (e.g. Google Drive) or a path to a PDF under `assets/resume/`. Used by the "View Resume" button. |
| `external-links` | Social/profile icon slugs (`linkedin`, `github`, `stackoverflow`, `medium`, `x-twitter`, `threads`, `instragram`, `youtube`, `discord`, `bluesky`). Enter only the **username/slug**, not the full URL. **Any entry left blank has its icon hidden** — only platforms with a value show up in the hero. |
| `skills` | List of `{ category, tools: [...] }` blocks rendered as the skills section. Indentation must be exact (2 spaces per nesting level) or the YAML won't parse. |
| `formspree-key` | Powers the contact form. Create a form at [formspree.io](https://formspree.io/), then paste the **8-character form id** (the part after `/f/` in your form's endpoint URL) in place of `YOUR_FORM_ID`. Until this is set, the contact form has nowhere to submit to. |

Build-related keys below the `#### Build settings` divider (`collections`, `markdown`, `kramdown`, `plugins`, `include`) generally shouldn't need to change.

## Adding a project

1. Create `_projects/<slug>/index.md`. Co-locate any images/videos for that project in the same folder.
2. Add front matter:

```yaml
---
layout: post
order: 10
title: Project title
description: One or two sentence summary shown on cards and the datasheet block.
skills:
  - Skill one
  - Skill two
main-image: /project.webp
featured: true
---
```

| Key | Required | Notes |
|---|---|---|
| `layout` | Yes | Always `post`. |
| `order` | Yes | Integer used to sort projects (ascending) on `/projects/` and for prev/next navigation on the project page. |
| `title` | Yes | Shown on project cards, the breadcrumb, and the `<h1>` on the project page. |
| `description` | Yes | Shown on project cards and in the "Overview" row of the project-page datasheet. |
| `skills` | Yes | List of tags. Cards show the first 4 with a `+N` overflow chip; the project page shows the full list as chips. |
| `main-image` | Yes | Filename **relative to the project's own folder** (e.g. `/robot-pick-place-still.jpg`), not a site-root path. If both a `.webp` and `.png` exist, an `onerror` fallback swaps extensions automatically if one 404s. |
| `featured` | No | `true` pins the project into the homepage's featured grid. If no projects set `featured: true`, the homepage falls back to the first 3 by `order`. |
| `published` | No | Set to `false` to hide a project from both the featured grid and the `/projects/` listing without deleting it. |
| `model` | No | Path to a `.glb`/`.gltf` file — renders an interactive 3D viewer above the article body. See [3D CAD model viewer](#3d-cad-model-viewer). |
| `role` | No | Shows a "Role" row in the project-page datasheet. |
| `year` | No | Shows a "Year" row in the project-page datasheet. |

1. Write the body in standard Markdown below the front matter. Two content includes are available:

   - **Image gallery** — comma-separated filenames relative to the project folder; multiple images in one call lay out as a row. Includes the same webp/png fallback swap as `main-image`.

     ```liquid
     {% include image-gallery.html images="photo1.jpg, photo2.png" height="350" %}
     ```

   - **YouTube embed** — pass the 11-character video id from the video's URL.

     ```liquid
     {% include youtube-video.html id="dQw4w9WgXcQ" autoplay="false" %}
     ```

   Regular Markdown extras also work: tables, fenced code blocks (syntax-highlighted via Rouge/kramdown — see `css/syntax.css`), blockquotes, and inline HTML (e.g. `<video>` tags, as used in `_projects/wordlebot/index.md`).

## 3D CAD model viewer

3D models are rendered with the [`<model-viewer>`](https://modelviewer.dev/) web component (loaded from a CDN in `_includes/model-viewer.html`), which supports orbit/zoom via mouse or touch and auto-rotation when idle.

There are two ways to embed a model:

- **Front matter** — set `model: /path/to/file.glb` on a project page; it renders automatically above the article content, captioned with the page title.
- **Inline, anywhere in a project body** — include it directly, wrapped in `{% raw %}...{% endraw %}` if you're pasting this literally into a Markdown file that might otherwise try to evaluate it:

  ```liquid
  {% include model-viewer.html src="/assets/models/part.glb" caption="ASSEMBLY — REV C" %}
  ```

  `src` accepts an absolute site path (`/assets/...`), a full URL, or (inside a project page) a filename relative to that project's folder. `caption`, `alt`, and `poster` (a preview image shown before the model loads) are all optional.

**Exporting a GLB from CAD:** most MCAD tools don't export GLB directly.

1. Export the part/assembly as **STEP** from SolidWorks, Inventor, etc.
2. Convert STEP → GLB using **Fusion 360** (import the STEP, then "Export" as GLB/OBJ), **Blender** (import STEP via a STEP-import add-on, then `File > Export > glTF 2.0`), or a dedicated converter such as CAD Exchanger.
3. Keep the exported file small — aim for **under ~5 MB** — and place it in `assets/models/` or directly in the project's own folder.

A working example lives at `assets/models/sample.glb`, wired up on the temporary `/model-test/` page (`model-test.html`, not linked from navigation). Delete `model-test.html` once real project models are in place and the viewer has been confirmed working on an actual project page.

## Experience timeline

Edit `_data/experience.yml`. Entries render newest-first via `_includes/timeline.html` on `/about/` (`about.md`).

| Field | Description |
|---|---|
| `date_range` | Free-text range, e.g. `"2024 — Present"`. |
| `title` | Role, position, or qualification title. |
| `org` | Organisation, team, or institution. |
| `type` | `work`, `team`, or `education` — shown as an uppercase chip next to `org`. |
| `summary` | 1–3 sentence description. |
| `bullets` | Optional list of supporting points. |

The current entries contain several `TODO(Connor)` comments (start dates, team name/scope, institution/degree wording) — confirm and remove these before treating the timeline as final.

## Design system

Tokens live at the top of `css/styles.css` (`:root`), pure CSS, no Liquid/build step involved.

**Palette** (dark, washed grey with a single blue accent):

| Token | Hex | Use |
|---|---|---|
| `--bg-0` | `#0e1114` | Page background. |
| `--bg-1` | `#15181c` | Slightly raised surfaces. |
| `--bg-2` | `#1c2126` | Further-raised surfaces (cards, etc.). |
| `--line` | `#262c32` | Subtle hairline borders. |
| `--line-strong` | `#3a424a` | Stronger borders (buttons, chips, dim-rule). |
| `--text-hi` | `#e9ecef` | Headings, high-emphasis text. |
| `--text` | `#b4bcc4` | Body text. |
| `--text-dim` | `#7e8790` | Kickers, captions, secondary text. |
| `--accent` | `#4da3ff` | Links, focus rings, solid-button fill. |
| `--accent-hi` | `#85c2ff` | Hover state for accent elements. |
| `--accent-wash` | `rgba(77,163,255,.10)` | Faint accent fill (chips, selection, hover backgrounds). |

**Type scale:** fluid, `clamp()`-based steps `--step--1` through `--step-3` (smallest to largest), so headings/body scale smoothly with viewport width instead of jumping at breakpoints.

**Spacing:** `--space-1` (0.25rem) through `--space-7` (6.5rem), used consistently instead of one-off margin/padding values.

**Fonts:** self-hosted, no external font CDN — `assets/fonts/InterVariable.woff2` (variable weight, UI font) and `assets/fonts/JetBrainsMono-{Regular,Medium}.woff2` (monospace, used for kickers, labels, chips, nav). Font Awesome (icons only) is still loaded from a CDN in `_layouts/wrapper.html`.

**Signature components:**

- `_includes/section-heading.html` — takes `number` (e.g. `"01"`) and `title`, and an optional `label`; renders a mono kicker + heading + a `.dim-rule` divider.
- `.dim-rule` — the horizontal "dimension line" divider (CSS-drawn, no image), optionally with a centered label.
- `.chip` — small mono-font pill used for skills tags.
- `.btn` / `.btn--solid` — outline and filled button styles.

**Scroll reveal:** add the `reveal` class to an element and it fades/slides in as it enters the viewport, implemented with pure CSS `animation-timeline: view()` (no JS/IntersectionObserver). It's scoped inside `@media (prefers-reduced-motion: no-preference)`, so it's automatically inert for users who prefer reduced motion, and degrades gracefully (element just renders normally) in browsers without scroll-driven-animation support.

**Changing the accent color:** edit the single `--accent` (and optionally `--accent-hi` / `--accent-wash`) token in `css/styles.css` — every link, focus ring, chip wash, and solid button derives from it.

## Changing the hero image

Replace `assets/images/hero/hero-space.jpg` (referenced from `_includes/hero.html` and preloaded in `_layouts/wrapper.html`). Recommended: ~1920px wide, kept well under ~400 KB for a fast LCP. A `hero-space-mobile.jpg` variant also exists in the same folder for future responsive use, though nothing in the current templates references it yet — wire it up (e.g. `<picture>`/`image-set()`) if you want an actual mobile-optimized swap.

Current hero image credit: NASA, ESA, CSA — *Cosmic Cliffs* (Carina Nebula), James Webb Space Telescope. NASA/ESA/CSA Webb imagery is public domain.

## Deployment

`.github/workflows/jekyll.yml` builds and deploys the site via GitHub Actions:

- Triggers on every push to `main`, and can also be run manually from the Actions tab (`workflow_dispatch`).
- Builds with `bundle exec jekyll build --baseurl "..."` under `JEKYLL_ENV=production`, then publishes `_site/` to GitHub Pages via `actions/deploy-pages`.
- Work-in-progress redesign work happens on the `redesign` branch; merge to `main` to trigger a production deploy.

## Credits

- Built on the [Free-To-Engineer](https://github.com/leea12/freeToEngineer) portfolio template, © [lowinertia.com](https://lowinertia.com), licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). The current site is a full visual and structural redesign of that template.
- Hero image: NASA, ESA, CSA — *Cosmic Cliffs* (Carina Nebula), James Webb Space Telescope (public domain).
- Fonts: [Inter](https://rsms.me/inter/) and [JetBrains Mono](https://www.jetbrains.com/lp/mono/), both self-hosted.
- Icons: [Font Awesome](https://fontawesome.com/).
- 3D viewer: [`<model-viewer>`](https://modelviewer.dev/) (Google).
