# Portfolio Linamaría — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a bilingual (ES/EN) personal portfolio for Linamaría Martínez using Astro 4 + Tailwind CSS with a GSAP scroll-curtain hero and IntersectionObserver reveals on all other sections.

**Architecture:** Static Astro site with 5 full-width sections (Hero, About, Projects, Skills, Contact), a floating language toggle backed by two JSON files and client-side JS, and a `[slug].astro` catch-all for project case studies. Grain texture is a fixed SVG overlay. Hero animation is GSAP ScrollTrigger only; all other reveals use IntersectionObserver.

**Tech Stack:** Astro 4, @astrojs/tailwind, Tailwind CSS, GSAP (gsap npm package), Google Fonts (DM Serif Display + DM Sans), vanilla JS, no TypeScript.

---

## File Map

| File | Responsibility |
|------|---------------|
| `astro.config.mjs` | Astro config, tailwind integration, static output |
| `tailwind.config.mjs` | Font families, color palette |
| `src/styles/global.css` | CSS custom properties, base resets, grain overlay, reveal classes, scroll arrow pulse |
| `src/layouts/Layout.astro` | HTML shell, Google Fonts, meta/SEO, grain overlay, i18n globals injected as `window.i18n` |
| `src/i18n/es.json` | All Spanish copy keyed by i18n key |
| `src/i18n/en.json` | All English copy keyed by i18n key |
| `src/components/LangToggle.astro` | Floating ES/EN button, reads/writes localStorage, swaps `data-i18n` elements |
| `src/components/Hero.astro` | 200vh wrapper + sticky inner, curtain halves, GSAP ScrollTrigger timeline |
| `src/components/About.astro` | Dark bg section, blockquote, 3 paragraphs, stats block, IntersectionObserver |
| `src/components/Projects.astro` | Lino bg section, 3 cards with stagger, IntersectionObserver |
| `src/components/Skills.astro` | Dark bg section, 6-card 2×3 grid, hover effects, IntersectionObserver |
| `src/components/Contact.astro` | Lino bg section, links, CV button, IntersectionObserver |
| `src/pages/index.astro` | Assembles all sections in order |
| `src/pages/proyectos/[slug].astro` | Static paths for 3 project slugs, placeholder page |
| `public/cv-placeholder.pdf` | Empty PDF placeholder |
| `README.md` | Setup instructions |

---

### Task 1: Scaffold Astro + Tailwind + GSAP

**Files:**
- Create: `astro.config.mjs`
- Create: `tailwind.config.mjs`
- Create: `package.json` (via npm create)

- [ ] **Step 1: Scaffold Astro minimal project**

```bash
cd /Users/linamariamartinez/Documents/programacion/portafolio-linamaria
npm create astro@latest . -- --template minimal --install --no-git --typescript disable
```

Accept all prompts (or pass `--yes` if supported). If interactive, choose: minimal template, no TypeScript, install dependencies.

- [ ] **Step 2: Add Tailwind integration**

```bash
npx astro add tailwind --yes
```

- [ ] **Step 3: Install GSAP**

```bash
npm install gsap
```

- [ ] **Step 4: Verify scaffold**

```bash
npm run build
```

Expected: Build completes with no errors. Output in `dist/`.

- [ ] **Step 5: Replace `tailwind.config.mjs` with project config**

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: ['./src/**/*.{astro,html,js,jsx,ts,tsx}'],
  theme: {
    extend: {
      fontFamily: {
        serif: ['"DM Serif Display"', 'serif'],
        sans: ['"DM Sans"', 'sans-serif'],
      },
      colors: {
        lino: '#F5F2EB',
        dark: '#1E1410',
        musgo: '#4A7C59',
        violeta: '#6B3FA0',
        terracota: '#C4622D',
        ambar: '#D4A853',
      },
    },
  },
  plugins: [],
};
```

- [ ] **Step 6: Replace `astro.config.mjs`**

```js
import { defineConfig } from 'astro/config';
import tailwind from '@astrojs/tailwind';

export default defineConfig({
  integrations: [tailwind()],
  output: 'static',
});
```

- [ ] **Step 7: Commit**

```bash
git init
git add .
git commit -m "chore: scaffold Astro + Tailwind + GSAP"
```

---

### Task 2: Global CSS + i18n JSON files

**Files:**
- Create: `src/styles/global.css`
- Create: `src/i18n/es.json`
- Create: `src/i18n/en.json`

- [ ] **Step 1: Create `src/styles/global.css`**

```css
@import url('https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Sans:wght@300;400;500;600&display=swap');

:root {
  --lino: #F5F2EB;
  --dark: #1E1410;
  --musgo: #4A7C59;
  --violeta: #6B3FA0;
  --terracota: #C4622D;
  --ambar: #D4A853;
  --text-dark: #1E1410;
  --text-light: #F5F2EB;
}

*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html {
  scroll-behavior: smooth;
}

body {
  font-family: 'DM Sans', sans-serif;
  color: var(--text-dark);
  overflow-x: hidden;
}

/* Grain overlay */
.grain-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 9999;
  opacity: 0.04;
}

/* Scroll reveals */
.reveal {
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.7s ease, transform 0.7s ease;
}
.reveal.visible {
  opacity: 1;
  transform: translateY(0);
}

/* Stagger delays */
.reveal-delay-1 { transition-delay: 0.1s; }
.reveal-delay-2 { transition-delay: 0.2s; }
.reveal-delay-3 { transition-delay: 0.3s; }
.reveal-delay-4 { transition-delay: 0.4s; }
.reveal-delay-5 { transition-delay: 0.5s; }
.reveal-delay-6 { transition-delay: 0.6s; }

/* Scroll arrow pulse */
@keyframes pulse-down {
  0%, 100% { transform: translateY(0); opacity: 1; }
  50% { transform: translateY(6px); opacity: 0.5; }
}
.scroll-arrow {
  animation: pulse-down 2s ease-in-out 1;
}

/* Small caps label */
.section-label {
  font-family: 'DM Sans', sans-serif;
  font-size: 11px;
  letter-spacing: 0.15em;
  text-transform: uppercase;
  font-variant: small-caps;
}
```

- [ ] **Step 2: Create `src/i18n/es.json`**

```json
{
  "hero.curtain": "Las personas te dicen lo que piensan.",
  "hero.curtain.left": "Las personas te dicen",
  "hero.curtain.right": "lo que piensan.",
  "hero.revealed": "Yo descubro por qué lo piensan.",
  "hero.name": "Linamaría Martínez",
  "hero.role": "Investigadora UX · IA con criterio humano",

  "about.name": "Linamaría Martínez",
  "about.quote": "La buena investigación no da respuestas. Cambia las preguntas que uno hace.",
  "about.quote.attr": "— Linamaría Martínez · Investigadora UX",
  "about.p1": "Pasé una década en campo — comunidades rurales, proyectos ambientales, planeación territorial. En Boyacá, Cundinamarca, Putumayo. Con CAR, Corpoboyacá, OPS/OMS. Escuchando a personas en contextos donde la versión oficial rara vez coincidía con lo que realmente pasaba.",
  "about.p2": "Ese trabajo me enseñó algo que ningún curso enseña: cómo quedarse en una pregunta el tiempo suficiente para entender qué se está preguntando de verdad. Cómo encontrar el patrón que cambia la interpretación de todo.",
  "about.p3": "En algún momento entendí que esas mismas preguntas aplican a productos digitales. Los métodos viajan. El rigor es el mismo. Lo que cambió es el medio — y las herramientas que uso ahora para construir, analizar y lanzar.",
  "about.stat1.num": "10+",
  "about.stat1.label": "años · investigación de campo",
  "about.stat2.num": "60+",
  "about.stat2.label": "talleres · facilitados",
  "about.stat3.num": "53K+",
  "about.stat3.label": "registros · analizados con ML",
  "about.stat4.num": "4",
  "about.stat4.label": "productos · construidos y lanzados",

  "projects.label": "Trabajo",
  "projects.intro": "Tres proyectos. Tres preguntas distintas.",
  "projects.cta": "Ver caso de estudio →",
  "projects.1.title": "Avifauna Biodiversidad — Calarcá, Quindío",
  "projects.1.desc": "ML aplicado a 53.963 registros abiertos para identificar clusters ecológicos y generar recomendaciones de conservación y aviturismo para stakeholders locales.",
  "projects.1.tags": "Machine Learning · Python · Datos Abiertos · Análisis de Stakeholders",
  "projects.2.title": "Event Master — Plataforma de Gestión de Eventos",
  "projects.2.desc": "Aplicación full-stack en producción que automatiza invitaciones digitales y gestión de RSVPs para agencia de eventos.",
  "projects.2.tags": "Full Stack · Next.js · Supabase · UX Design",
  "projects.3.title": "Oracle Alumnithon 2025 — 🏆 2.º lugar",
  "projects.3.desc": "Identidad visual en Figma + autenticación JWT, perfiles dinámicos y dashboard por roles en React 18 + TypeScript.",
  "projects.3.tags": "Frontend · React · TypeScript · Hackathon",

  "skills.label": "Capacidades",
  "skills.1.intention": "Entender lo que las personas realmente necesitan",
  "skills.1.tools": "Entrevistas · Etnografía · Talleres · Síntesis · Miro",
  "skills.2.intention": "Encontrar patrones en datos complejos",
  "skills.2.tools": "Python · pandas · scikit-learn · K-Means · EDA · SQL",
  "skills.3.intention": "Construir lo que investigo",
  "skills.3.tools": "React · Next.js · TypeScript · Supabase · REST APIs",
  "skills.4.intention": "Hacer preguntas que cambian la dirección",
  "skills.4.tools": "Investigación cualitativa · Desk research · Mapeo de experiencia · Análisis de stakeholders",
  "skills.5.intention": "Integrar IA con criterio humano",
  "skills.5.tools": "LLMs (Claude · ChatGPT · Gemini) · LangChain · n8n · Agentes",
  "skills.6.intention": "Traducir hallazgos en decisiones",
  "skills.6.tools": "Facilitación · Comunicación técnica/no técnica · Gestión de proyectos · Stakeholder management",

  "contact.label": "Hablemos",
  "contact.headline": "Si estás trabajando en algo donde entender a las personas marca la diferencia — quiero escucharlo.",
  "contact.cv": "Descargar CV",
  "contact.note": "Disponible para investigación UX, consultoría en IA y trabajo de producto centrado en personas. Remoto. Global.",

  "slug.wip": "Caso de estudio en construcción. Vuelve pronto.",
  "slug.back": "← Volver",

  "toggle.es": "ES",
  "toggle.en": "EN"
}
```

- [ ] **Step 3: Create `src/i18n/en.json`**

```json
{
  "hero.curtain": "People tell you what they think.",
  "hero.curtain.left": "People tell you",
  "hero.curtain.right": "what they think.",
  "hero.revealed": "I discover why they think it.",
  "hero.name": "Linamaría Martínez",
  "hero.role": "UX Researcher · Human-Centered AI",

  "about.name": "Linamaría Martínez",
  "about.quote": "Good research doesn't give answers. It changes the questions you ask.",
  "about.quote.attr": "— Linamaría Martínez · UX Researcher",
  "about.p1": "I spent a decade in the field — rural communities, environmental projects, territorial planning. In Boyacá, Cundinamarca, Putumayo. With CAR, Corpoboyacá, PAHO/WHO. Listening to people in contexts where the official version rarely matched what was actually happening.",
  "about.p2": "That work taught me something no course teaches: how to stay with a question long enough to understand what's really being asked. How to find the pattern that changes the interpretation of everything.",
  "about.p3": "At some point I understood that those same questions apply to digital products. Methods travel. Rigor is the same. What changed is the medium — and the tools I now use to build, analyze, and ship.",
  "about.stat1.num": "10+",
  "about.stat1.label": "years · field research",
  "about.stat2.num": "60+",
  "about.stat2.label": "workshops · facilitated",
  "about.stat3.num": "53K+",
  "about.stat3.label": "records · analyzed with ML",
  "about.stat4.num": "4",
  "about.stat4.label": "products · built and shipped",

  "projects.label": "Work",
  "projects.intro": "Three projects. Three different questions.",
  "projects.cta": "View case study →",
  "projects.1.title": "Avifauna Biodiversity — Calarcá, Quindío",
  "projects.1.desc": "ML applied to 53,963 open records to identify ecological clusters and generate conservation and birdwatching recommendations for local stakeholders.",
  "projects.1.tags": "Machine Learning · Python · Open Data · Stakeholder Analysis",
  "projects.2.title": "Event Master — Event Management Platform",
  "projects.2.desc": "Full-stack production app that automates digital invitations and RSVP management for an events agency.",
  "projects.2.tags": "Full Stack · Next.js · Supabase · UX Design",
  "projects.3.title": "Oracle Alumnithon 2025 — 🏆 2nd place",
  "projects.3.desc": "Visual identity in Figma + JWT auth, dynamic profiles, and role-based dashboard in React 18 + TypeScript.",
  "projects.3.tags": "Frontend · React · TypeScript · Hackathon",

  "skills.label": "Capabilities",
  "skills.1.intention": "Understand what people really need",
  "skills.1.tools": "Interviews · Ethnography · Workshops · Synthesis · Miro",
  "skills.2.intention": "Find patterns in complex data",
  "skills.2.tools": "Python · pandas · scikit-learn · K-Means · EDA · SQL",
  "skills.3.intention": "Build what I research",
  "skills.3.tools": "React · Next.js · TypeScript · Supabase · REST APIs",
  "skills.4.intention": "Ask questions that change direction",
  "skills.4.tools": "Qualitative research · Desk research · Experience mapping · Stakeholder analysis",
  "skills.5.intention": "Integrate AI with human judgment",
  "skills.5.tools": "LLMs (Claude · ChatGPT · Gemini) · LangChain · n8n · Agents",
  "skills.6.intention": "Translate findings into decisions",
  "skills.6.tools": "Facilitation · Technical/non-technical communication · Project management · Stakeholder management",

  "contact.label": "Let's talk",
  "contact.headline": "If you're working on something where understanding people makes a difference — I want to hear about it.",
  "contact.cv": "Download CV",
  "contact.note": "Available for UX research, AI consulting, and human-centered product work. Remote. Global.",

  "slug.wip": "Case study in progress. Come back soon.",
  "slug.back": "← Back",

  "toggle.es": "ES",
  "toggle.en": "EN"
}
```

- [ ] **Step 4: Verify build**

```bash
npm run build
```

Expected: No errors.

---

### Task 3: Layout.astro

**Files:**
- Modify: `src/layouts/Layout.astro`

- [ ] **Step 1: Write `src/layouts/Layout.astro`**

```astro
---
import '../styles/global.css';
import LangToggle from '../components/LangToggle.astro';
import es from '../i18n/es.json';
import en from '../i18n/en.json';
---

<!DOCTYPE html>
<html lang="es" data-lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="description" content="Linamaría Martínez — Investigadora UX y especialista en IA con criterio humano. Más de 10 años de investigación cualitativa de campo en Colombia." />
  <meta property="og:title" content="Linamaría Martínez — Investigadora UX · IA con criterio humano" />
  <meta property="og:description" content="Investigadora UX y especialista en IA con criterio humano. Más de 10 años en campo, ahora en tech." />
  <meta property="og:type" content="website" />
  <title>Linamaría Martínez — Investigadora UX · IA con criterio humano</title>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Sans:opsz,wght@9..40,300;9..40,400;9..40,500;9..40,600&display=swap" rel="stylesheet" />
</head>
<body>

  <!-- Grain overlay -->
  <div class="grain-overlay" aria-hidden="true">
    <svg xmlns="http://www.w3.org/2000/svg" width="100%" height="100%">
      <filter id="grain-noise">
        <feTurbulence type="fractalNoise" baseFrequency="0.65" numOctaves="3" stitchTiles="stitch"/>
        <feColorMatrix type="saturate" values="0"/>
      </filter>
      <rect width="100%" height="100%" filter="url(#grain-noise)"/>
    </svg>
  </div>

  <LangToggle />

  <slot />

  <script define:vars={{ es, en }}>
    window.i18n = { es, en };

    function applyLang(lang) {
      document.documentElement.setAttribute('data-lang', lang);
      document.documentElement.lang = lang;
      document.querySelectorAll('[data-i18n]').forEach(el => {
        const key = el.getAttribute('data-i18n');
        const val = window.i18n[lang]?.[key];
        if (val !== undefined) el.textContent = val;
      });
      document.querySelectorAll('[data-i18n-placeholder]').forEach(el => {
        const key = el.getAttribute('data-i18n-placeholder');
        const val = window.i18n[lang]?.[key];
        if (val !== undefined) el.placeholder = val;
      });
    }

    const saved = localStorage.getItem('lang') || 'es';
    applyLang(saved);

    window.setLang = function(lang) {
      localStorage.setItem('lang', lang);
      applyLang(lang);
    };
  </script>

</body>
</html>
```

- [ ] **Step 2: Verify build**

```bash
npm run build
```

Expected: No errors. Layout compiles cleanly.

---

### Task 4: LangToggle.astro

**Files:**
- Create: `src/components/LangToggle.astro`

- [ ] **Step 1: Write `src/components/LangToggle.astro`**

```astro
---
---

<button
  id="lang-toggle"
  aria-label="Cambiar idioma / Switch language"
  style="
    position: fixed;
    top: 1.25rem;
    right: 1.25rem;
    z-index: 1000;
    background: transparent;
    border: 1px solid var(--musgo);
    color: var(--musgo);
    font-family: 'DM Sans', sans-serif;
    font-size: 12px;
    letter-spacing: 0.1em;
    padding: 0.4rem 0.75rem;
    cursor: pointer;
    transition: background 0.2s, color 0.2s;
  "
>
  <span id="lang-es">ES</span>
  <span style="opacity:0.4; margin: 0 0.25rem;">/</span>
  <span id="lang-en">EN</span>
</button>

<script>
  const btn = document.getElementById('lang-toggle');
  const spanEs = document.getElementById('lang-es');
  const spanEn = document.getElementById('lang-en');

  function updateToggleStyle(lang) {
    if (lang === 'es') {
      spanEs.style.fontWeight = '600';
      spanEs.style.opacity = '1';
      spanEn.style.fontWeight = '400';
      spanEn.style.opacity = '0.45';
    } else {
      spanEn.style.fontWeight = '600';
      spanEn.style.opacity = '1';
      spanEs.style.fontWeight = '400';
      spanEs.style.opacity = '0.45';
    }
  }

  const saved = localStorage.getItem('lang') || 'es';
  updateToggleStyle(saved);

  btn.addEventListener('click', () => {
    const current = localStorage.getItem('lang') || 'es';
    const next = current === 'es' ? 'en' : 'es';
    window.setLang(next);
    updateToggleStyle(next);
  });

  btn.addEventListener('mouseenter', () => {
    btn.style.background = 'var(--musgo)';
    btn.style.color = 'var(--text-light)';
  });
  btn.addEventListener('mouseleave', () => {
    btn.style.background = 'transparent';
    btn.style.color = 'var(--musgo)';
  });
</script>
```

---

### Task 5: Hero.astro (GSAP ScrollTrigger curtain)

**Files:**
- Create: `src/components/Hero.astro`

- [ ] **Step 1: Write `src/components/Hero.astro`**

```astro
---
---

<section
  class="hero-wrapper"
  style="height: 200vh; position: relative; background: var(--lino);"
>
  <div
    class="hero-sticky"
    style="
      position: sticky;
      top: 0;
      height: 100vh;
      overflow: hidden;
      display: flex;
      align-items: center;
      justify-content: center;
    "
  >
    <!-- Curtain left half -->
    <div
      class="curtain-half curtain-left"
      style="
        position: absolute;
        top: 0; left: 0;
        width: 50%; height: 100%;
        background: var(--lino);
        display: flex;
        align-items: center;
        justify-content: flex-end;
        padding-right: 0.5rem;
        z-index: 10;
        overflow: hidden;
      "
    >
      <span
        data-i18n="hero.curtain.left"
        style="
          font-family: 'DM Serif Display', serif;
          font-size: clamp(1.5rem, 4vw, 2.5rem);
          color: var(--text-dark);
          white-space: nowrap;
        "
      >Las personas te dicen</span>
    </div>

    <!-- Curtain right half -->
    <div
      class="curtain-half curtain-right"
      style="
        position: absolute;
        top: 0; right: 0;
        width: 50%; height: 100%;
        background: var(--lino);
        display: flex;
        align-items: center;
        justify-content: flex-start;
        padding-left: 0.5rem;
        z-index: 10;
        overflow: hidden;
      "
    >
      <span
        data-i18n="hero.curtain.right"
        style="
          font-family: 'DM Serif Display', serif;
          font-size: clamp(1.5rem, 4vw, 2.5rem);
          color: var(--text-dark);
          white-space: nowrap;
        "
      >lo que piensan.</span>
    </div>

    <!-- Revealed phrase (behind curtain) -->
    <div
      class="hero-revealed"
      style="
        position: absolute;
        opacity: 0;
        text-align: center;
        padding: 0 2rem;
        z-index: 5;
      "
    >
      <p
        data-i18n="hero.revealed"
        style="
          font-family: 'DM Serif Display', serif;
          font-size: clamp(1.75rem, 5vw, 3rem);
          color: var(--violeta);
          line-height: 1.2;
        "
      >Yo descubro por qué lo piensan.</p>
    </div>

    <!-- Identity block -->
    <div
      class="hero-identity"
      style="
        position: absolute;
        bottom: 12vh;
        left: 50%;
        transform: translateX(-50%) translateY(20px);
        opacity: 0;
        text-align: center;
        z-index: 5;
        width: 100%;
      "
    >
      <div style="width: 52px; height: 1px; background: var(--terracota); margin: 0 auto 1.5rem;"></div>
      <h1
        data-i18n="hero.name"
        style="
          font-family: 'DM Serif Display', serif;
          font-size: 52px;
          color: var(--text-dark);
          line-height: 1.1;
        "
      >Linamaría Martínez</h1>
      <p
        data-i18n="hero.role"
        style="
          font-family: 'DM Sans', sans-serif;
          font-size: 14px;
          color: var(--musgo);
          margin-top: 0.75rem;
          letter-spacing: 0.05em;
        "
      >Investigadora UX · IA con criterio humano</p>
      <div class="scroll-arrow" style="margin-top: 2.5rem; color: var(--terracota); font-size: 1.25rem;">↓</div>
    </div>

  </div>
</section>

<script>
  import gsap from 'gsap';
  import { ScrollTrigger } from 'gsap/ScrollTrigger';

  gsap.registerPlugin(ScrollTrigger);

  const tl = gsap.timeline({
    scrollTrigger: {
      trigger: '.hero-wrapper',
      start: 'top top',
      end: 'bottom bottom',
      scrub: 1,
    }
  });

  tl
    .to('.curtain-left',  { x: '-100%', ease: 'none', duration: 1 }, 0)
    .to('.curtain-right', { x: '100%',  ease: 'none', duration: 1 }, 0)
    .to('.hero-revealed', { opacity: 1, ease: 'none', duration: 0.4 }, 0.4)
    .to('.hero-identity', {
      opacity: 1,
      y: 0,
      ease: 'none',
      duration: 0.4,
    }, 0.7);
</script>

<style>
  @media (max-width: 640px) {
    .curtain-left span,
    .curtain-right span {
      font-size: 1.25rem !important;
      white-space: normal !important;
    }
  }
</style>
```

- [ ] **Step 2: Verify build**

```bash
npm run build
```

Expected: No errors. GSAP imports resolve correctly.

---

### Task 6: About.astro

**Files:**
- Create: `src/components/About.astro`

- [ ] **Step 1: Write `src/components/About.astro`**

```astro
---
---

<section style="background: var(--dark); padding: 8rem 2rem;">
  <div style="max-width: 760px; margin: 0 auto;">

    <!-- Name anchor -->
    <h2
      data-i18n="about.name"
      class="reveal"
      style="
        font-family: 'DM Serif Display', serif;
        font-size: 38px;
        color: var(--ambar);
        margin-bottom: 2.5rem;
      "
    >Linamaría Martínez</h2>

    <!-- Editorial blockquote -->
    <blockquote
      class="reveal"
      style="
        border-left: 3px solid var(--violeta);
        padding-left: 1.5rem;
        margin: 0 0 3rem;
      "
    >
      <p
        data-i18n="about.quote"
        style="
          font-family: 'DM Serif Display', serif;
          font-size: 1.25rem;
          color: var(--text-light);
          font-style: italic;
          line-height: 1.6;
        "
      >La buena investigación no da respuestas. Cambia las preguntas que uno hace.</p>
      <footer
        data-i18n="about.quote.attr"
        style="
          margin-top: 0.75rem;
          font-size: 11px;
          color: var(--musgo);
          font-family: 'DM Sans', sans-serif;
          letter-spacing: 0.05em;
        "
      >— Linamaría Martínez · Investigadora UX</footer>
    </blockquote>

    <!-- Paragraphs -->
    <p
      data-i18n="about.p1"
      class="reveal"
      style="
        font-family: 'DM Sans', sans-serif;
        font-size: 1rem;
        color: var(--text-light);
        opacity-color: 0.85;
        line-height: 1.8;
        margin-bottom: 1.5rem;
      "
    >Pasé una década en campo — comunidades rurales, proyectos ambientales, planeación territorial. En Boyacá, Cundinamarca, Putumayo. Con CAR, Corpoboyacá, OPS/OMS. Escuchando a personas en contextos donde la versión oficial rara vez coincidía con lo que realmente pasaba.</p>

    <p
      data-i18n="about.p2"
      class="reveal"
      style="
        font-family: 'DM Sans', sans-serif;
        font-size: 1rem;
        color: var(--text-light);
        line-height: 1.8;
        margin-bottom: 1.5rem;
      "
    >Ese trabajo me enseñó algo que ningún curso enseña: cómo quedarse en una pregunta el tiempo suficiente para entender qué se está preguntando de verdad. Cómo encontrar el patrón que cambia la interpretación de todo.</p>

    <p
      data-i18n="about.p3"
      class="reveal"
      style="
        font-family: 'DM Sans', sans-serif;
        font-size: 1rem;
        color: var(--text-light);
        line-height: 1.8;
        margin-bottom: 4rem;
      "
    >En algún momento entendí que esas mismas preguntas aplican a productos digitales. Los métodos viajan. El rigor es el mismo. Lo que cambió es el medio — y las herramientas que uso ahora para construir, analizar y lanzar.</p>

    <!-- Stat block -->
    <div style="display: grid; grid-template-columns: repeat(2, 1fr); gap: 2rem;">
      <div class="reveal reveal-delay-1">
        <div data-i18n="about.stat1.num" style="font-family: 'DM Serif Display', serif; font-size: 2.5rem; color: var(--ambar);">10+</div>
        <div data-i18n="about.stat1.label" style="font-family: 'DM Sans', sans-serif; font-size: 0.8rem; color: var(--text-light); opacity: 0.6; margin-top: 0.25rem;">años · investigación de campo</div>
      </div>
      <div class="reveal reveal-delay-2">
        <div data-i18n="about.stat2.num" style="font-family: 'DM Serif Display', serif; font-size: 2.5rem; color: var(--ambar);">60+</div>
        <div data-i18n="about.stat2.label" style="font-family: 'DM Sans', sans-serif; font-size: 0.8rem; color: var(--text-light); opacity: 0.6; margin-top: 0.25rem;">talleres · facilitados</div>
      </div>
      <div class="reveal reveal-delay-3">
        <div data-i18n="about.stat3.num" style="font-family: 'DM Serif Display', serif; font-size: 2.5rem; color: var(--ambar);">53K+</div>
        <div data-i18n="about.stat3.label" style="font-family: 'DM Sans', sans-serif; font-size: 0.8rem; color: var(--text-light); opacity: 0.6; margin-top: 0.25rem;">registros · analizados con ML</div>
      </div>
      <div class="reveal reveal-delay-4">
        <div data-i18n="about.stat4.num" style="font-family: 'DM Serif Display', serif; font-size: 2.5rem; color: var(--ambar);">4</div>
        <div data-i18n="about.stat4.label" style="font-family: 'DM Sans', sans-serif; font-size: 0.8rem; color: var(--text-light); opacity: 0.6; margin-top: 0.25rem;">productos · construidos y lanzados</div>
      </div>
    </div>

  </div>
</section>

<script>
  const observer = new IntersectionObserver(
    (entries) => entries.forEach(e => { if (e.isIntersecting) e.target.classList.add('visible'); }),
    { threshold: 0.15 }
  );
  document.querySelectorAll('#about-section .reveal').forEach(el => observer.observe(el));
</script>
```

---

### Task 7: Projects.astro + [slug].astro

**Files:**
- Create: `src/components/Projects.astro`
- Create: `src/pages/proyectos/[slug].astro`

- [ ] **Step 1: Write `src/components/Projects.astro`**

```astro
---
const projects = [
  {
    slug: 'avifauna',
    titleKey: 'projects.1.title',
    descKey: 'projects.1.desc',
    tagsKey: 'projects.1.tags',
    titleEs: 'Avifauna Biodiversidad — Calarcá, Quindío',
    descEs: 'ML aplicado a 53.963 registros abiertos para identificar clusters ecológicos y generar recomendaciones de conservación y aviturismo para stakeholders locales.',
    tagsEs: 'Machine Learning · Python · Datos Abiertos · Análisis de Stakeholders',
    featured: true,
  },
  {
    slug: 'event-master',
    titleKey: 'projects.2.title',
    descKey: 'projects.2.desc',
    tagsKey: 'projects.2.tags',
    titleEs: 'Event Master — Plataforma de Gestión de Eventos',
    descEs: 'Aplicación full-stack en producción que automatiza invitaciones digitales y gestión de RSVPs para agencia de eventos.',
    tagsEs: 'Full Stack · Next.js · Supabase · UX Design',
    featured: false,
  },
  {
    slug: 'oracle-alumnithon',
    titleKey: 'projects.3.title',
    descKey: 'projects.3.desc',
    tagsKey: 'projects.3.tags',
    titleEs: 'Oracle Alumnithon 2025 — 🏆 2.º lugar',
    descEs: 'Identidad visual en Figma + autenticación JWT, perfiles dinámicos y dashboard por roles en React 18 + TypeScript.',
    tagsEs: 'Frontend · React · TypeScript · Hackathon',
    featured: false,
  },
];
---

<section style="background: var(--lino); padding: 8rem 2rem;">
  <div style="max-width: 900px; margin: 0 auto;">

    <p class="section-label reveal" style="color: var(--musgo); margin-bottom: 1rem;" data-i18n="projects.label">Trabajo</p>
    <h2 class="reveal" data-i18n="projects.intro" style="
      font-family: 'DM Serif Display', serif;
      font-size: clamp(1.75rem, 4vw, 2.5rem);
      color: var(--text-dark);
      margin-bottom: 4rem;
      line-height: 1.2;
    ">Tres proyectos. Tres preguntas distintas.</h2>

    <div style="display: flex; flex-direction: column; gap: 1.5rem;">
      {projects.map((p, i) => (
        <div
          class={`reveal reveal-delay-${i + 1}`}
          style={`
            border: 1px solid #E0D8CE;
            padding: ${p.featured ? '2.5rem' : '2rem'};
            background: var(--lino);
            transition: box-shadow 0.2s;
          `}
        >
          <h3
            data-i18n={p.titleKey}
            style={`
              font-family: 'DM Serif Display', serif;
              font-size: ${p.featured ? '1.5rem' : '1.2rem'};
              color: var(--text-dark);
              margin-bottom: 0.75rem;
            `}
          >{p.titleEs}</h3>
          <p
            data-i18n={p.descKey}
            style="
              font-family: 'DM Sans', sans-serif;
              font-size: 0.95rem;
              color: var(--text-dark);
              opacity: 0.8;
              line-height: 1.7;
              margin-bottom: 1rem;
            "
          >{p.descEs}</p>
          <p
            data-i18n={p.tagsKey}
            style="
              font-family: 'DM Sans', sans-serif;
              font-size: 0.75rem;
              color: var(--musgo);
              letter-spacing: 0.03em;
              margin-bottom: 1.25rem;
            "
          >{p.tagsEs}</p>
          <a
            href={`/proyectos/${p.slug}`}
            style="
              font-family: 'DM Sans', sans-serif;
              font-size: 0.875rem;
              color: var(--violeta);
              text-decoration: none;
            "
            onmouseover="this.style.textDecoration='underline'"
            onmouseout="this.style.textDecoration='none'"
          ><span data-i18n="projects.cta">Ver caso de estudio →</span></a>
        </div>
      ))}
    </div>

  </div>
</section>

<script>
  const obs = new IntersectionObserver(
    (entries) => entries.forEach(e => { if (e.isIntersecting) e.target.classList.add('visible'); }),
    { threshold: 0.1 }
  );
  document.querySelectorAll('#projects-section .reveal, #projects-section [class*="reveal"]').forEach(el => obs.observe(el));
</script>
```

- [ ] **Step 2: Write `src/pages/proyectos/[slug].astro`**

```astro
---
import Layout from '../../layouts/Layout.astro';

export function getStaticPaths() {
  return [
    {
      params: { slug: 'avifauna' },
      props: {
        titleEs: 'Avifauna Biodiversidad — Calarcá, Quindío',
        descEs: 'ML aplicado a 53.963 registros abiertos para identificar clusters ecológicos y generar recomendaciones de conservación y aviturismo para stakeholders locales.',
        tagsEs: 'Machine Learning · Python · Datos Abiertos · Análisis de Stakeholders',
        titleKey: 'projects.1.title',
        descKey: 'projects.1.desc',
        tagsKey: 'projects.1.tags',
      }
    },
    {
      params: { slug: 'event-master' },
      props: {
        titleEs: 'Event Master — Plataforma de Gestión de Eventos',
        descEs: 'Aplicación full-stack en producción que automatiza invitaciones digitales y gestión de RSVPs para agencia de eventos.',
        tagsEs: 'Full Stack · Next.js · Supabase · UX Design',
        titleKey: 'projects.2.title',
        descKey: 'projects.2.desc',
        tagsKey: 'projects.2.tags',
      }
    },
    {
      params: { slug: 'oracle-alumnithon' },
      props: {
        titleEs: 'Oracle Alumnithon 2025 — 🏆 2.º lugar',
        descEs: 'Identidad visual en Figma + autenticación JWT, perfiles dinámicos y dashboard por roles en React 18 + TypeScript.',
        tagsEs: 'Frontend · React · TypeScript · Hackathon',
        titleKey: 'projects.3.title',
        descKey: 'projects.3.desc',
        tagsKey: 'projects.3.tags',
      }
    },
  ];
}

const { titleEs, descEs, tagsEs, titleKey, descKey, tagsKey } = Astro.props;
---

<Layout>
  <main style="
    min-height: 100vh;
    background: var(--lino);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 4rem 2rem;
    text-align: center;
  ">
    <a
      href="/"
      data-i18n="slug.back"
      style="
        font-family: 'DM Sans', sans-serif;
        font-size: 0.875rem;
        color: var(--violeta);
        text-decoration: none;
        margin-bottom: 3rem;
        display: block;
      "
    >← Volver</a>

    <h1
      data-i18n={titleKey}
      style="
        font-family: 'DM Serif Display', serif;
        font-size: clamp(1.5rem, 4vw, 2.5rem);
        color: var(--text-dark);
        margin-bottom: 1.5rem;
        max-width: 700px;
        line-height: 1.2;
      "
    >{titleEs}</h1>

    <p
      data-i18n={descKey}
      style="
        font-family: 'DM Sans', sans-serif;
        font-size: 1rem;
        color: var(--text-dark);
        opacity: 0.75;
        line-height: 1.8;
        max-width: 600px;
        margin-bottom: 1rem;
      "
    >{descEs}</p>

    <p
      data-i18n={tagsKey}
      style="
        font-family: 'DM Sans', sans-serif;
        font-size: 0.75rem;
        color: var(--musgo);
        margin-bottom: 3rem;
      "
    >{tagsEs}</p>

    <div style="width: 52px; height: 1px; background: var(--terracota); margin: 0 auto 2rem;"></div>

    <p
      data-i18n="slug.wip"
      style="
        font-family: 'DM Serif Display', serif;
        font-size: 1.25rem;
        color: var(--text-dark);
        opacity: 0.6;
      "
    >Caso de estudio en construcción. Vuelve pronto.</p>
  </main>
</Layout>
```

---

### Task 8: Skills.astro

**Files:**
- Create: `src/components/Skills.astro`

- [ ] **Step 1: Write `src/components/Skills.astro`**

```astro
---
const skills = [
  { num: 1, intentionKey: 'skills.1.intention', toolsKey: 'skills.1.tools',
    intentionEs: 'Entender lo que las personas realmente necesitan',
    toolsEs: 'Entrevistas · Etnografía · Talleres · Síntesis · Miro' },
  { num: 2, intentionKey: 'skills.2.intention', toolsKey: 'skills.2.tools',
    intentionEs: 'Encontrar patrones en datos complejos',
    toolsEs: 'Python · pandas · scikit-learn · K-Means · EDA · SQL' },
  { num: 3, intentionKey: 'skills.3.intention', toolsKey: 'skills.3.tools',
    intentionEs: 'Construir lo que investigo',
    toolsEs: 'React · Next.js · TypeScript · Supabase · REST APIs' },
  { num: 4, intentionKey: 'skills.4.intention', toolsKey: 'skills.4.tools',
    intentionEs: 'Hacer preguntas que cambian la dirección',
    toolsEs: 'Investigación cualitativa · Desk research · Mapeo de experiencia · Análisis de stakeholders' },
  { num: 5, intentionKey: 'skills.5.intention', toolsKey: 'skills.5.tools',
    intentionEs: 'Integrar IA con criterio humano',
    toolsEs: 'LLMs (Claude · ChatGPT · Gemini) · LangChain · n8n · Agentes' },
  { num: 6, intentionKey: 'skills.6.intention', toolsKey: 'skills.6.tools',
    intentionEs: 'Traducir hallazgos en decisiones',
    toolsEs: 'Facilitación · Comunicación técnica/no técnica · Gestión de proyectos · Stakeholder management' },
];
---

<section style="background: var(--dark); padding: 8rem 2rem;">
  <div style="max-width: 900px; margin: 0 auto;">

    <p class="section-label reveal" data-i18n="skills.label" style="color: var(--ambar); margin-bottom: 3rem;">Capacidades</p>

    <div style="display: grid; grid-template-columns: repeat(2, 1fr); gap: 1.5rem;">
      {skills.map((s, i) => (
        <div
          class={`skill-card reveal reveal-delay-${i + 1}`}
          style="
            background: #2A1E18;
            border: 1px solid rgba(74, 124, 89, 0.3);
            padding: 1.75rem;
            transition: border-color 0.25s;
            cursor: default;
          "
        >
          <h3
            data-i18n={s.intentionKey}
            class="skill-title"
            style="
              font-family: 'DM Serif Display', serif;
              font-size: 1.05rem;
              color: var(--text-light);
              margin-bottom: 0.75rem;
              line-height: 1.4;
              transition: color 0.25s;
            "
          >{s.intentionEs}</h3>
          <p
            data-i18n={s.toolsKey}
            style="
              font-family: 'DM Sans', sans-serif;
              font-size: 0.75rem;
              color: var(--text-light);
              opacity: 0.45;
              line-height: 1.6;
            "
          >{s.toolsEs}</p>
        </div>
      ))}
    </div>

  </div>
</section>

<style>
  .skill-card:hover {
    border-color: var(--musgo) !important;
  }
  .skill-card:hover .skill-title {
    color: var(--ambar) !important;
  }

  @media (max-width: 640px) {
    div[style*="grid-template-columns: repeat(2"] {
      grid-template-columns: 1fr !important;
    }
  }
</style>

<script>
  const obs = new IntersectionObserver(
    (entries) => entries.forEach(e => { if (e.isIntersecting) e.target.classList.add('visible'); }),
    { threshold: 0.1 }
  );
  document.querySelectorAll('.skill-card').forEach(el => obs.observe(el));
</script>
```

---

### Task 9: Contact.astro + index.astro

**Files:**
- Create: `src/components/Contact.astro`
- Modify: `src/pages/index.astro`

- [ ] **Step 1: Write `src/components/Contact.astro`**

```astro
---
---

<section style="background: var(--lino); padding: 8rem 2rem;">
  <div style="max-width: 680px; margin: 0 auto; text-align: center;">

    <p class="section-label reveal" data-i18n="contact.label" style="color: var(--musgo); margin-bottom: 1.5rem;">Hablemos</p>

    <h2 class="reveal" data-i18n="contact.headline" style="
      font-family: 'DM Serif Display', serif;
      font-size: clamp(1.5rem, 3.5vw, 2rem);
      color: var(--text-dark);
      line-height: 1.4;
      margin-bottom: 3rem;
    ">Si estás trabajando en algo donde entender a las personas marca la diferencia — quiero escucharlo.</h2>

    <div class="reveal" style="display: flex; flex-direction: column; gap: 1rem; align-items: center; margin-bottom: 2.5rem;">
      <a
        href="mailto:linamaria.dev@gmail.com"
        style="
          font-family: 'DM Sans', sans-serif;
          font-size: 1rem;
          color: var(--text-dark);
          text-decoration: none;
          border-bottom: 1px solid var(--musgo);
          padding-bottom: 2px;
        "
      >linamaria.dev@gmail.com</a>
      <a
        href="#"
        style="
          font-family: 'DM Sans', sans-serif;
          font-size: 0.9rem;
          color: var(--text-dark);
          text-decoration: none;
          opacity: 0.6;
        "
      >LinkedIn</a>
      <a
        href="#"
        style="
          font-family: 'DM Sans', sans-serif;
          font-size: 0.9rem;
          color: var(--text-dark);
          text-decoration: none;
          opacity: 0.6;
        "
      >GitHub</a>
    </div>

    <a
      href="/cv-placeholder.pdf"
      download
      class="reveal cv-btn"
      style="
        display: inline-flex;
        align-items: center;
        gap: 0.5rem;
        font-family: 'DM Sans', sans-serif;
        font-size: 0.875rem;
        color: var(--terracota);
        border: 1px solid var(--terracota);
        padding: 0.75rem 1.75rem;
        text-decoration: none;
        transition: background 0.2s, color 0.2s;
        margin-bottom: 2.5rem;
      "
    >
      <span data-i18n="contact.cv">Descargar CV</span>
      <span>↓</span>
    </a>

    <p class="reveal" data-i18n="contact.note" style="
      font-family: 'DM Sans', sans-serif;
      font-size: 12px;
      color: var(--text-dark);
      opacity: 0.5;
      line-height: 1.6;
    ">Disponible para investigación UX, consultoría en IA y trabajo de producto centrado en personas. Remoto. Global.</p>

  </div>
</section>

<style>
  .cv-btn:hover {
    background: var(--terracota) !important;
    color: var(--text-light) !important;
  }
</style>

<script>
  const obs = new IntersectionObserver(
    (entries) => entries.forEach(e => { if (e.isIntersecting) e.target.classList.add('visible'); }),
    { threshold: 0.1 }
  );
  document.querySelectorAll('#contact-section .reveal').forEach(el => obs.observe(el));
</script>
```

- [ ] **Step 2: Write `src/pages/index.astro`**

```astro
---
import Layout from '../layouts/Layout.astro';
import Hero from '../components/Hero.astro';
import About from '../components/About.astro';
import Projects from '../components/Projects.astro';
import Skills from '../components/Skills.astro';
import Contact from '../components/Contact.astro';
---

<Layout>
  <main>
    <div id="hero-section"><Hero /></div>
    <div id="about-section"><About /></div>
    <div id="projects-section"><Projects /></div>
    <div id="skills-section"><Skills /></div>
    <div id="contact-section"><Contact /></div>
  </main>
</Layout>

<script>
  // Global IntersectionObserver for all .reveal elements not handled by component scripts
  const obs = new IntersectionObserver(
    (entries) => entries.forEach(e => { if (e.isIntersecting) e.target.classList.add('visible'); }),
    { threshold: 0.1 }
  );
  document.querySelectorAll('.reveal').forEach(el => obs.observe(el));
</script>
```

- [ ] **Step 3: Create `public/cv-placeholder.pdf`**

Create empty file:
```bash
echo "" > public/cv-placeholder.pdf
```

- [ ] **Step 4: Verify build**

```bash
npm run build
```

Expected: Build succeeds, `dist/` has `index.html` and `proyectos/avifauna/index.html`, etc.

---

### Task 10: README + final verification

**Files:**
- Create: `README.md`

- [ ] **Step 1: Write README.md**

```markdown
# Portafolio · Linamaría Martínez

Portfolio personal — Astro 4 + Tailwind CSS.

## Requisitos

Node 18+

## Instalación

npm install

## Desarrollo

npm run dev

## Producción

npm run build
npm run preview
```

- [ ] **Step 2: Start dev server and verify**

```bash
npm run dev
```

Expected: Dev server starts at `http://localhost:4321`. Visit in browser and verify:
- Hero curtain animates on scroll
- Language toggle works (ES ↔ EN)
- All 5 sections render
- Project links go to placeholder pages
- Grain overlay visible at 4% opacity
- Mobile layout correct

- [ ] **Step 3: Final commit**

```bash
git add .
git commit -m "feat: complete portfolio — Astro + Tailwind + GSAP"
```
