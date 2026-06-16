uild a personal portfolio website using Astro + Tailwind CSS.
Deploy-ready, Spanish by default with English toggle.


PERSON

Linamaría Martínez — UX Researcher | Human-Centered AI
Más de 10 años de investigación cualitativa de campo en Colombia
→ transición al mundo tech. Combina metodología de investigación
profunda con ejecución técnica (ML, full-stack). Perfil híbrido:
lidera desde el pensamiento investigativo, lo respalda con
capacidad técnica.


DESIGN SYSTEM

Fonts: DM Serif Display (headings) + DM Sans (body) — Google Fonts

Colors:
--lino:      #F5F2EB
--dark:      #1E1410
--musgo:     #4A7C59
--violeta:   #6B3FA0
--terracota: #C4622D
--ambar:     #D4A853
--text-dark: #1E1410
--text-light:#F5F2EB

No neon. No glassmorphism. No particles.
Grain texture overlay on all sections (SVG noise, 4% opacity).
Scroll-reveal: fade + translateY(20px) → 0 on all sections.


PAGE STRUCTURE

Alternating backgrounds:
Hero → lino | About → dark | Projects → lino |
Skills → dark | Contact → lino


SECTION 1 — HERO (bg: lino)

Scroll-pinned curtain animation (height: 200vh, sticky):

Phase 1 — full screen, centered:
"Las personas te dicen lo que piensan"
Split into left/right halves, ready to open.

Phase 2 — on scroll, halves slide horizontally outward:
left half → translateX(-100%), right half → translateX(+100%)
Behind them, revealed:
"Yo descubro por qué lo piensan"
Color: --violeta

Phase 3 — after curtain fully open, fade in:
Name: "Linamaría Martínez"
→ DM Serif Display, 52px, --text-dark
Roles: "Investigadora UX · IA con criterio humano"
→ DM Sans, 14px, --musgo
Thin line 52px, --terracota, centered between phrase and name.
Scroll indicator: ↓ arrow, --terracota, pulses once.

Animation: scroll-linked via GSAP ScrollTrigger.
NOT IntersectionObserver for this section.


SECTION 2 — ABOUT (bg: dark)

Opens with name as anchor:
"Linamaría Martínez"
→ DM Serif Display, 38px, --ambar

Editorial blockquote, left border 3px --violeta:
"La buena investigación no da respuestas.
Cambia las preguntas que uno hace."
Attribution: "— Linamaría Martínez · Investigadora UX"
→ 11px, --musgo

3 paragraphs, each reveals individually on scroll:

P1: "Pasé una década en campo — comunidades rurales, proyectos
ambientales, planeación territorial. En Boyacá, Cundinamarca,
Putumayo. Con CAR, Corpoboyacá, OPS/OMS. Escuchando a personas
en contextos donde la versión oficial rara vez coincidía
con lo que realmente pasaba."

P2: "Ese trabajo me enseñó algo que ningún curso enseña: cómo
quedarse en una pregunta el tiempo suficiente para entender
qué se está preguntando de verdad. Cómo encontrar el patrón
que cambia la interpretación de todo."

P3: "En algún momento entendí que esas mismas preguntas aplican
a productos digitales. Los métodos viajan. El rigor es el mismo.
Lo que cambió es el medio — y las herramientas que uso ahora
para construir, analizar y lanzar."

Stat block (staggered reveal):
Numbers: --ambar · Labels: --text-light at 60% opacity

10+ años · investigación de campo
60+ talleres · facilitados
53K+ registros · analizados con ML
4 productos · construidos y lanzados


SECTION 3 — PROJECTS (bg: lino)

Section label: "Trabajo" (small caps, --musgo)
Intro: "Tres proyectos. Tres preguntas distintas."
→ DM Serif Display, --text-dark

3 cards, staggered scroll reveal, border 1px #E0D8CE.
Card 1 slightly larger/more prominent.

Card 1:
Title: "Avifauna Biodiversidad — Calarcá, Quindío"
Desc: "ML aplicado a 53.963 registros abiertos para identificar
clusters ecológicos y generar recomendaciones de conservación
y aviturismo para stakeholders locales."
Tags: Machine Learning · Python · Datos Abiertos ·
Análisis de Stakeholders

Card 2:
Title: "Event Master — Plataforma de Gestión de Eventos"
Desc: "Aplicación full-stack en producción que automatiza
invitaciones digitales y gestión de RSVPs para agencia de eventos."
Tags: Full Stack · Next.js · Supabase · UX Design

Card 3:
Title: "Oracle Alumnithon 2025 — 🏆 2.º lugar"
Desc: "Identidad visual en Figma + autenticación JWT, perfiles
dinámicos y dashboard por roles en React 18 + TypeScript."
Tags: Frontend · React · TypeScript · Hackathon

Each card: CTA "Ver caso de estudio →" (--violeta, hover underline)
Links to /proyectos/[slug] — placeholder page:
Same design, title, description, tags, and centered text:
"Caso de estudio en construcción. Vuelve pronto."
← Volver link.


SECTION 4 — SKILLS (bg: dark)

Section label: "Capacidades" (small caps, --ambar)

6 cards, 2×3 grid desktop / single column mobile.
Card bg: #2A1E18, border 1px --musgo at 30% opacity.
Hover: border → full --musgo, title → --ambar.

Each card: intention (large) + tools (small, muted).


"Entender lo que las personas realmente necesitan"
Entrevistas · Etnografía · Talleres · Síntesis · Miro
"Encontrar patrones en datos complejos"
Python · pandas · scikit-learn · K-Means · EDA · SQL
"Construir lo que investigo"
React · Next.js · TypeScript · Supabase · REST APIs
"Hacer preguntas que cambian la dirección"
Investigación cualitativa · Desk research ·
Mapeo de experiencia · Análisis de stakeholders
"Integrar IA con criterio humano"
LLMs (Claude · ChatGPT · Gemini) · LangChain · n8n · Agentes
"Traducir hallazgos en decisiones"
Facilitación · Comunicación técnica/no técnica ·
Gestión de proyectos · Stakeholder management



SECTION 5 — CONTACT (bg: lino)

Section label: "Hablemos" (small caps, --musgo)

Headline (DM Serif Display, 32px, --text-dark):
"Si estás trabajando en algo donde entender a las personas
marca la diferencia — quiero escucharlo."

Links:
Email: linamaria.dev@gmail.com
LinkedIn: [placeholder]
GitHub: [placeholder]

Button "Descargar CV":
Outlined, --terracota border, fill on hover, ↓ icon.
Links to /cv placeholder.

Note (12px, muted):
"Disponible para investigación UX, consultoría en IA
y trabajo de producto centrado en personas. Remoto. Global."


LANGUAGE TOGGLE

Floating button top-right: "ES / EN"
Default: Spanish.
Toggle swaps all text to English.
Implement with Astro i18n content collections:
src/i18n/es.json + src/i18n/en.json


TECHNICAL


Astro 4+ static output
Tailwind CSS for layout and spacing
CSS custom properties for color system (defined above)
Google Fonts: DM Serif Display + DM Sans
GSAP ScrollTrigger for Hero curtain only (pnpm add gsap)
Intersection Observer for all other scroll reveals
Mobile-first responsive
SEO: meta title, description, og:title, og:description in Spanish
/public/cv-placeholder.pdf as empty placeholder
README.md with: pnpm install, pnpm dev, pnpm build
Use pnpm throughout — do not use npm or yarn
No TypeScript required
