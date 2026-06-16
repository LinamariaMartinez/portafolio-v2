# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
pnpm dev        # dev server → http://localhost:4321
pnpm build      # static build → dist/
pnpm preview    # preview built output
```

**Use pnpm only — never npm or yarn.**

## Architecture

Single-page Astro site with static output. One page (`src/pages/index.astro`) composed of sequential full-viewport sections. Dynamic project pages at `/proyectos/[slug]` (3 slugs: `avifauna`, `event-master`, `oracle-alumnithon`) — currently WIP placeholders.

### Section order & backgrounds

| Section | Component | Background |
|---|---|---|
| Hero | `Hero.astro` | `--lino` |
| About | `About.astro` | `--dark` |
| Projects | `Projects.astro` | `--lino` |
| Skills | `Skills.astro` | `--dark` |
| Contact | `Contact.astro` | `--lino` |

### i18n system

**Client-side only — no Astro i18n integration.** `Layout.astro` injects both `es.json` and `en.json` as `window.i18n`. Language state lives in `localStorage('lang')`, default `es`. `window.setLang(lang)` swaps all `[data-i18n]` attribute values at runtime. Any new translatable text needs a key in **both** `src/i18n/es.json` and `src/i18n/en.json`, and a `data-i18n="key"` attribute on the element.

### Animations

- **Hero only**: GSAP ScrollTrigger — scroll-pinned curtain that splits and reveals (`hero-wrapper` is `200vh` tall, sticky). Do not switch Hero to IntersectionObserver.
- **All other sections**: IntersectionObserver via `.reveal` class (`src/styles/global.css`). Add `class="reveal"` to any element that should fade+rise on scroll. Stagger with `reveal-delay-1` through `reveal-delay-6`.

### Design system

CSS custom properties defined in `src/styles/global.css` (`:root`) and mirrored as Tailwind v4 theme tokens (`@theme`):

```
--lino: #F5F2EB  (warm off-white, primary bg)
--dark: #1E1410  (near-black, alternate sections)
--musgo: #4A7C59  (green, labels, muted)
--violeta: #6B3FA0  (purple, accents, links)
--terracota: #C4622D  (rust-orange, highlights)
--ambar: #D4A853  (amber, numbers, dark-section headings)
```

Fonts: `DM Serif Display` (headings) + `DM Sans` (body) — loaded from Google Fonts in `Layout.astro`.

Grain overlay: fixed SVG noise layer at 4% opacity, `z-index: 9999`, pointer-events none — defined in Layout, styled in `global.css`. Do not remove.

### Adding a real case study

When a project slug gets real content, replace the WIP page in `src/pages/proyectos/[slug].astro` — it currently shows a placeholder for all three slugs. The `getStaticPaths` props (`titleKey`, `descKey`, `tagsKey`) already wire up i18n.
