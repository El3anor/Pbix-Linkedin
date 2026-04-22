---
name: wireframe-to-mockup
description: >
  Transforms a hand-drawn wireframe (photo or sketch) into a fully functional, interactive HTML/React mockup.
  Use this skill whenever the user uploads a photo of a paper wireframe, napkin sketch, or hand-drawn UI layout
  and wants to generate a working prototype from it. Trigger also for requests like "turn this sketch into a
  prototype", "build this from my wireframe", "code this mockup", or "create a functional version of this drawing".
  This skill is especially well-suited for dashboards, admin panels, data tools, and analytics UIs with KPI cards,
  charts, tables, and interactive components.
---

# Wireframe → Functional Mockup

You have received a photo of a hand-drawn wireframe. Your job is to produce a polished, interactive HTML/React
artifact that faithfully implements the layout while elevating it into a production-quality UI.

---

## Step 1 — Analyze, Clarify & Confirm (before writing any code)

### 1a — Wireframe recap (always first)

Before asking anything, present a structured recap of what you understood from the wireframe:

- **Sections identifiées** : list every panel, card group, chart, table, and navigation element you can see
- **Labels lus** : list all text labels decoded from the handwriting
- **Structure** : describe the layout (e.g. "header + 4 KPI cards en ligne + 2 panneaux en bas à gauche + 1 panneau à droite")
- **Ambiguïtés** : flag anything you couldn't read or weren't sure about

Keep this recap concise and structured — it's for the user to validate before you ask anything.

---

### 1b — Clarification des ambiguïtés (only if ambiguities exist)

If you flagged any ambiguities in the recap, ask about them **before** the standard questions — one at a time.

For each ambiguity:
- State what you saw: *"J'ai vu un élément en bas à droite avec du texte illisible — c'est quoi ?"*
- Wait for the answer before moving to the next ambiguity or to the standard questions
- After all ambiguities are resolved, update your internal understanding of the wireframe before continuing

If there are no ambiguities, skip this step entirely and go straight to 1c.

---

### 1c — Questions de clarification (one at a time, ALWAYS before any code)

After the recap, ask the four questions below **strictly one at a time** — send each question, wait for the answer, then send the next. Never group them.

**Question 1 — Interactions**
Ask openly, with zero suggestion or example:
> "Quelles interactions souhaites-tu sur cette maquette ?"
Do NOT hint at any pattern (no "les cartes sont-elles cliquables ?", no examples). Wait for the answer, then ask Q2.

**Question 2 — Data**
> "Y a-t-il des précisions sur la donnée ? (valeurs réelles, formats, unités, période affichée…)"
Wait for the answer, then ask Q3.

**Question 3 — Couleurs**
> "Quelle direction de couleurs souhaites-tu ? (ex : palette de ta marque, tons clairs/sombres, couleur dominante…)"
Wait for the answer, then ask Q4.

**Question 4 — Autre**
> "Autre chose à préciser ou ajouter qui n'est pas visible sur le wireframe ?"

If the user answers "rien", "go ahead", or similar to any question, treat it as no input and move to the next question immediately.

---

### 1c — Confirmation before coding

After all four answers, present a brief synthesis:
- Recap interactions, data notes, color direction, and any extras in a few bullet points
- Then ask: **"Je peux lancer la création de la maquette ?"**

Only start coding after an explicit confirmation (e.g. "oui", "go", "c'est bon").

---

## Step 2 — Design Direction

Apply the following rules for **dashboard / analytics UIs** (the primary use case of this skill).
Adjust if the wireframe is clearly something else (form, landing page, etc.).

### Colors
- Use the color direction provided by the user in Question 3 as the primary guide
- If no direction given, default to: background `#F4F5F7`, cards white, accent indigo `#4F46E5`
- Cards/panels: white with subtle shadow (`box-shadow: 0 1px 4px rgba(0,0,0,0.08)`)
- Positive variance: green (`#16A34A`) — Negative variance: red (`#DC2626`)
- Text hierarchy: `#111827` (primary), `#6B7280` (secondary/labels), `#9CA3AF` (muted)

### Typography
Use only fonts that are **pre-installed in Power BI** and optimized for numeric legibility:
- **Segoe UI** (default, always available in Power BI) — preferred for body, labels, and tables
- **DIN** or **Verdana** as alternatives — both have clear, distinct digits (0–9 never ambiguous)
- Never use decorative, condensed, or script fonts
- Digits must be tabular (fixed-width) so columns of numbers align vertically — Segoe UI satisfies this natively
- KPI values: `font-size: 2rem–3rem`, `font-weight: 700`
- Card labels: `font-size: 0.7rem`, `letter-spacing: 0.06em`, `text-transform: uppercase`, color muted
- Body / table: `font-size: 0.85rem`, `font-weight: 400`
- Load via Google Fonts only if Segoe UI is unavailable; otherwise use the system stack: `font-family: 'Segoe UI', 'Verdana', sans-serif`

### Spacing & Layout
- **The panels must collectively form a single tight rectangle** — no panel shorter, narrower, or offset from its neighbours. Imagine a bounding box around all panels: every panel must touch that box on its outer edge, and all inner gaps must be uniform.
  - Use a single CSS Grid on the root container with explicit `grid-template-columns` and `grid-template-rows`
  - Set `align-items: stretch` and `justify-items: stretch` so every cell is fully occupied
  - Every panel uses `height: 100%` and `width: 100%` — no fixed pixel heights
  - The root container uses `width: 100%` and fills the available vertical space
  - Panels that span multiple columns or rows must use `grid-column: span N` / `grid-row: span N` — never absolute positioning
- Outer padding: `24px` (on the root container only — not on individual panels)
- Gap between panels: `16px–20px` (uniform — same value horizontally and vertically)
- Card inner padding: `20px–24px`
- Border radius: `10px–12px` for cards, `8px` for inner elements
- Use CSS Grid for the main layout (never floats, never flexbox at the top level)

### Chart style
- Use **Chart.js** (CDN) for line charts and bar charts
- Remove chart borders and use light gridlines (`rgba(0,0,0,0.05)`)
- Show 3 series max on a line chart: Actuel (solid accent), Target (dashed orange), Last Year (dashed grey)
- Table rows: `border-bottom: 1px solid #F3F4F6`, no outer borders
- Inline progress bars in tables: `height: 6px`, `border-radius: 3px`, background `#E5E7EB`, fill with accent color

---

## Step 3 — Interactivity

**Read the wireframe first.** Interactivity is inferred from the drawing — do not apply a fixed pattern.

Ask yourself:
- Are visual connections drawn between components? (arrow, alignment, shared label) → they are linked
- Does a component appear in multiple states? (e.g. one card outlined, others plain) → it's a selector
- Is a chart or table positioned below/beside a group of cards? → the cards likely filter it
- Are there tabs, toggles, or segmented controls sketched? → implement them as-is

**If the user provides a completed mockup image as reference** (second image), treat it as the authoritative spec for interactions — replicate exactly what it shows.

**If interactions are genuinely ambiguous**, implement the most minimal version (e.g. static) and add a comment in the code: `/* TODO: clarify interaction with user */`. Do not invent complex behaviors that aren't suggested.

### Always apply regardless
- Navigation header: logo placeholder top left, period/date top right, no routing needed
- Active/selected states get the primary accent color (filled background + white text)
- Animate chart data updates: `animation: { duration: 400 }` in Chart.js

---

## Step 4 — Data

**Extract data labels and structure directly from the wireframe.** The wireframe is the source of truth.

- Read every label, column header, row name, and axis annotation visible in the drawing
- Use those exact names in the code (respecting the language of the wireframe — French stays French)
- Infer the data type from the component: cards → single KPI value + delta; tables → rows × columns; charts → time series or categories

**Fill in the actual numbers** with realistic placeholder values coherent with the domain and the scale suggested by the wireframe. For example:
- A KPI card labelled "Effectif" with "123 Δ—" → use a plausible headcount number (e.g. 327) and a % variance
- A table labelled "Services" with 4 rows → create 4 realistic department rows

**Do not invent new labels or columns** that are not present or strongly implied by the wireframe. If a section is blank or generic (e.g. just "123"), choose a sensible placeholder value but keep the structure minimal.

---

## Step 5 — Output Format

Produce a **single self-contained HTML file** with:
- All CSS in a `<style>` tag
- All JS in a `<script>` tag at the bottom
- Chart.js loaded from CDN: `https://cdn.jsdelivr.net/npm/chart.js`
- Google Fonts loaded via `<link>`
- No external dependencies beyond Chart.js and Google Fonts

Do **not** produce a React artifact unless the user explicitly asks for React.

---

## Quality Checklist (verify before presenting)

- [ ] Wireframe recap was presented before any questions
- [ ] Each ambiguity was clarified one at a time before standard questions (if any existed)
- [ ] All four standard clarification questions were asked one at a time
- [ ] User confirmed before coding started
- [ ] All wireframe sections are represented (none skipped)
- [ ] Labels match the wireframe exactly (language, names, column headers)
- [ ] Interactions are exactly what the user described — no invented behaviors
- [ ] Colors reflect the user's direction from Question 3
- [ ] Active/selected states use the primary accent color
- [ ] Data values are plausible for the domain and scale
- [ ] Font is Segoe UI (or Verdana) — no decorative fonts, digits are tabular and clearly legible
- [ ] All panels together form a single tight rectangle — no panel is shorter, narrower, or offset
- [ ] Typography hierarchy is clear
- [ ] Spacing is uniform — same gap horizontally and vertically

---

## Edge Cases

- **Unreadable text in wireframe**: make your best guess and state the assumption
- **Ambiguous layout**: default to a 3-column grid for the top row, 2-column for the bottom row
- **Missing chart type**: default to a line chart for time-series KPIs, bar chart for categorical
- **Single KPI only**: still make the card "active" with the accent style, but skip the click interaction
