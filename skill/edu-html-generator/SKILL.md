---
name: edu-html-generator
description: >
  Generate complete, single-file educational HTML pages for Swiss vocational and language education,
  and for any school level from primary to college. Use this skill whenever a teacher wants to create
  interactive lessons, print documents, info dossiers, adaptive learning paths, case studies,
  vocabulary trainers, concept maps, lab protocols, timelines, roleplay cards, or exit tickets.
  Trigger on phrases like: "make me a lesson about X", "create an interactive HTML for X",
  "build a lesson from this transcript", "generate a dossier about X", "create a print worksheet for X",
  "make an interactive lesson", "HTML lesson", "edu page", "Lernpfad", "Fallstudie", "Vokabeltrainer",
  "Concept Map", "Laborprotokoll", "Zeitleiste", "Rollenspiel", "Exit Ticket", or any request combining
  a topic with a classroom or student context. Always use this skill — do not freestyle educational HTML
  without it.
---

# edu-html-generator

Generates production-quality, single-file educational HTML pages in **11 types** across two groups:

**Original Types (A–C)** — Swiss vocational / ABU Reform 2030
- **Type A** — Interactive Lesson (slide-based, game-like, dark/light toggle)
- **Type B** — Print-First Document (A4 pages, HKO brochure style)
- **Type C** — Info Dossier (scroll-through, Tailwind CDN, media links)

**New Types (E–M)** — All school levels, all subjects
- **Type E** — Adaptive Lernpfad (branching, differentiation)
- **Type F** — Fallstudie / Case Study (tabs, documents, decision)
- **Type G** — Vocabulary Trainer (flip → MC → typing rounds)
- **Type H** — Concept Map Builder (SVG canvas, drag & drop)
- **Type I** — Experiment Log (lab protocol, data table)
- **Type J** — Timeline Builder (order activity + display)
- **Type K** — Rollenspiel-Karten (persona cards, random draw)
- **M** — Exit Ticket (3–5 slides, ampel self-assessment)

**Always read this file fully before generating. Follow the Interview → Decide Type → Generate flow.**

---

## Step 1: Interview the Teacher

Ask all required questions at once, conversationally. Extract answers from any transcript/article provided before asking.

**Always required:**
1. **Topic / input** — Free description, transcript, article, or topic name?
2. **Language** — German / English / other?
3. **Audience** — Age, level (A1–C1 / Lehrjahr / grade), school type
4. **Page type** — Which type? (or suggest based on input — see Step 2)
5. **Mode** — Self-paced / Teacher-led / Hybrid?

**Type-specific extras — ask only for the chosen type:**

| Type | Extra questions |
|------|----------------|
| A | Components (see Component Menu), font, accent color, student name modal |
| B | Situation color (A–E), number of pages, sections needed, any URLs for QR codes |
| C | Media links, number of fact cards, timeline needed? |
| E | Learning objectives (2–3), branch depth, entry diagnostic? |
| F | Scenario, documents available, decision task, student role/perspective |
| G | Word list (or generate from topic), rounds (flip/MC/typing), language direction |
| H | Seed concepts (4–8), mode (free/guided/display), print needed? |
| I | Subject, sections needed, number of data table rows |
| J | Events list (or generate), mode (order/display/both), era/date range |
| K | Scenario, roles (how many + perspectives), task type, random draw + timer? |
| M | 1–3 questions, ampel self-assessment, anonymous or named? |

---

## Step 2: Decide the Page Type

| Signal | Suggest Type |
|--------|-------------|
| "interactive", "quiz", "game", "activity", "self-paced" | A |
| Transcript / news article + "lesson" | A |
| "brochure", "print", "ABU", "Lernsituation", "worksheet" | B |
| "dossier", "overview", "news", "Überblick" | C |
| "branching", "adaptive", "Lernpfad", "differentiation" | E |
| "case study", "Fallstudie", "scenario", "documents" | F |
| "vocabulary", "Vokabeltrainer", "spaced repetition", "Wörter üben" | G |
| "concept map", "Mindmap", "Begriffe vernetzen" | H |
| "experiment", "lab", "Protokoll", "Versuch" | I |
| "timeline", "Zeitleiste", "chronology", "ordnen" | J |
| "roleplay", "Rollenspiel", "Persona", "Planspiel" | K |
| "exit ticket", "Check-in", "Lernstandscheck", "quick check" | M |
| Teacher-led classroom display | A or C |

When unclear, suggest Type A and confirm.

---

## Step 3: Generate the HTML

**Single file. All CSS and JS embedded. No external dependencies except Google Fonts and optional cdnjs.**

### For Types A, B, C — use the reference files:
- `references/type-a-components.md` — Full component code for Type A
- `references/type-b-components.md` — Print document components
- `references/type-c-components.md` — Dossier components

### For Types E–M — use the template files as your base:

Each template in `assets/templates/` is a **complete, working HTML file** with `[Platzhalter]` markers everywhere content must be replaced. Your job:
1. Read the relevant template
2. Replace ALL `[...]` placeholders with real content
3. Populate the JS DATA constants at the top with teacher-provided content
4. Adjust colors, language, structure as needed

| Type | Template file |
|------|--------------|
| E | `assets/templates/type-e-adaptive-lernpfad.html` |
| F | `assets/templates/type-f-fallstudie.html` |
| G | `assets/templates/type-g-vocabulary-trainer.html` |
| H | `assets/templates/type-h-concept-map.html` |
| I | `assets/templates/type-i-experiment-log.html` |
| J | `assets/templates/type-j-timeline.html` |
| K | `assets/templates/type-k-rollenspiel.html` |
| M | `assets/templates/type-m-exit-ticket.html` |

**Template usage pattern:**
```
1. view assets/templates/type-[X]-[name].html  ← read it fully
2. Replace [Platzhalter] with real content
3. Populate DATA constants (VOCAB, NODES, EVENTS, ROLES, etc.)
4. Output as single complete HTML file
```

---

## Type A — Architecture Summary

Full code in `references/type-a-components.md`.

### Universal Rules
- All content in JS DATA constants — never hardcode in HTML
- **Dark/light toggle** always present (moon/sun icon)
- **🖨️ Ausdruck** button always in header (always visible)
- **Sticky bottom nav** always present (← Zurück | counter | Weiter →)
- **← → keyboard navigation** (skips inputs/textareas)
- **localStorage** saves all state after every interaction

### CSS Variable System
```css
:root[data-theme="dark"] { --bg-primary:#0f172a; --bg-secondary:#1e293b; --bg-card:#334155; --text-primary:#f1f5f9; --text-secondary:#94a3b8; --border:#475569; }
:root[data-theme="light"] { --bg-primary:#f8fafc; --bg-secondary:#ffffff; --bg-card:#f1f5f9; --text-primary:#1e293b; --text-secondary:#64748b; --border:#e2e8f0; }
:root { --accent:[color]; --accent-2:[color]; --green:#4ade80; --red:#f87171; --yellow:#fbbf24; --radius:12px; --radius-lg:20px; --transition:0.2s ease; }
```

### Component Menu (Type A)
| Component | Notes |
|-----------|-------|
| Vocab flip cards | Front-in-flow/back-absolute CSS — see reference |
| Multiple choice | Store chosen **index**, not boolean |
| Fill-in-blank | Word chips + input |
| Yes/No | Store chosen **boolean** |
| Drag & drop | Match to categories |
| Open text / reflection | Auto-saved to localStorage |
| Discussion prompts | Talk cards |
| Reading passage | Hover tooltips |
| Results slide | Stars + print + reset |

### Vocab Flip Card — Critical CSS
```css
/* FRONT: normal flow — sets height */
.vocab-front { min-height:150px; /* ... */ backface-visibility:hidden; }
/* BACK: absolute overlay */
.vocab-back { position:absolute; inset:0; transform:rotateY(180deg); backface-visibility:hidden; }
/* Grid: FIXED columns, not auto-fit */
.vocab-grid { display:grid; grid-template-columns:repeat(3,1fr); gap:14px; }
```

---

## Type B — Architecture Summary

Full code in `references/type-b-components.md`.

- A4 pages as `.page` divs (210mm × 297mm)
- DM Serif Display + DM Sans + DM Mono
- Situation color via `--sit-color` (A→`#C0392B` B→`#2471A3` C→`#1E8449` D→`#D35400` E→`#7D3C98`)
- **QR codes (REQUIRED)** for every URL/video: use `qrcode.js` from cdnjs

---

## Type E — Adaptive Lernpfad Summary

**Architecture:** Node-graph rendered as sequential screens. Types: `intro | question | explanation | checkpoint | results`

**Key rules:**
- Wrong answer → explanation branch → retry question
- After 2 wrong attempts: **fail-safe forward** (never leave student stuck)
- No back-navigation through branch nodes
- localStorage stores: completed node IDs, retry counts, question answers, reflection text

**DATA structure:**
```js
const NODES = [
  { id:'start', type:'intro', title:'', text:'', next:'q1' },
  { id:'q1', type:'question', question:'', choices:[], correct:0, onCorrect:'q2', onWrong:'help1' },
  { id:'help1', type:'explanation', title:'', text:'', visual:'', next:'q1retry' },
  { id:'q1retry', type:'question', /* ... */ onCorrect:'q2', onWrong:'q2' /* fail-safe */ },
  { id:'results', type:'results' }
];
```

---

## Type F — Fallstudie Summary

**Architecture:** 5 tabs, **sequentially unlocking** (Situation → Dokumente → Aufgaben → Entscheidung → Reflexion)

**Document card types:** `letter` (serif, paper-style) | `stat` (CSS bar chart) | `contract` (monospace) | `news` (2-column) | `quote` (pull-quote)

**Decision tab:** structured argument builder (Position → Arg1 → Arg2 → Gegenargument → Fazit)

---

## Type G — Vocabulary Trainer Summary

**Architecture:** 3 rounds — Flip Cards → Multiple Choice → Typing

**Typing round:** Levenshtein distance ≤1 → "Fast richtig ✓" (partial credit). Enter key submits.

**DATA:**
```js
const VOCAB = [{ word:'', emoji:'', translation:'', example:'' }];
const CONFIG = { lang1:'Deutsch', lang2:'Englisch', rounds:['flip','mc','typing'], direction:'L1toL2' };
```

---

## Type H — Concept Map Builder Summary

**Architecture:** SVG overlay for arrows + absolutely positioned div nodes. Canvas-based, full-viewport.

**Modes:** `free` (student builds) | `guided` (preloaded nodes, student adds labels) | `display` (teacher-built, view only)

**Interaction:** Drag nodes from sidebar → drop on canvas. Connect mode: click source → click target → prompt for label.

---

## Type I — Experiment Log Summary

**Architecture:** Scrolling form, always light (print accuracy). No dark mode.

**Sections:** Hypothesis (word count) | Material (checklist) | Procedure (step checkboxes) | Observation | Data table (auto-average) | Conclusion | Error analysis

**Print:** Native browser print, full A4 protocol layout.

---

## Type J — Timeline Builder Summary

**Architecture:** 2 tabs — Order mode (drag & drop) + Display mode (interactive, click for details)

**Order mode:** Shuffled cards → student drags to slots → Check button validates → score + red/green feedback

**Display mode:** Category filter buttons, click event to expand detail.

---

## Type K — Rollenspiel-Karten Summary

**Architecture:** Deck view (all cards) + Draw view (random reveal). No slides.

**Draw view:** Random card flip animation → **countdown timer** (default 2 min, configurable via `ROLEPLAY.prepTime`)

**DATA:**
```js
const ROLEPLAY = {
  roles: [{ id:'r1', name:'', emoji:'', job:'', position:'', arguments:[], goal:'', secret:'', color:'#6366f1' }]
};
```

**Print:** 2 cards per A4 page, A5 format.

---

## Type M — Exit Ticket Summary

**Architecture:** Type A slide shell, max 6 slides, stripped (no score badge, no dots).

**Fixed structure:** Welcome → Q1 (MC) → Q2 (open) → Q3 (YN) → Ampel → Danke/Print

**No feedback shown** — purely formative. Ampel: 🔴 Ich brauche Hilfe | 🟡 Ich bin unsicher | 🟢 Ich hab's verstanden

**Print:** Half-page A5 landscape, 2 tickets per A4.

---

## Quality Checklist — All Types

**Universal:**
- [ ] All `[Platzhalter]` replaced with real content
- [ ] Content in JS DATA constants, not hardcoded HTML
- [ ] Fully self-contained single file
- [ ] Google Fonts import present
- [ ] Print button always visible in header
- [ ] localStorage saves/restores all state
- [ ] Dark/light toggle (except Type B and I — always light)
- [ ] Responsive at 600px

**Type A specific:**
- [ ] Vocab uses front-in-flow / back-absolute CSS
- [ ] Vocab grid uses `repeat(3, 1fr)` fixed columns
- [ ] MC answers stored as **index**, YN as **boolean**
- [ ] Sticky bottom nav, keyboard nav, `nav-buttons { display:none }`
- [ ] `main-container` has `padding-bottom: 90px`
- [ ] Results slide has 🔄 Neu starten button
- [ ] Print includes all answer types

**Type B specific:**
- [ ] QR codes for every URL via qrcode.js
- [ ] A4 page divs, mm units, `@page { size:A4 portrait; margin:0 }`

**Type E specific:**
- [ ] Fail-safe forward after 2 wrong attempts
- [ ] Branch arrows invisible to student
- [ ] Explanation nodes ≤80 words

**Type F specific:**
- [ ] Tabs unlock sequentially
- [ ] Argument builder: Position + 2 Args + Gegenargument + Fazit

**Type G specific:**
- [ ] Levenshtein tolerance in typing round
- [ ] Words shuffled each session (Fisher-Yates)

**Type H specific:**
- [ ] Undo (Ctrl+Z) works
- [ ] Connection labels editable by clicking

**Type I specific:**
- [ ] Always light (no dark mode)
- [ ] Data table auto-calculates average

**Type J specific:**
- [ ] "Zeige Lösung" after 2 failed attempts (order mode)

**Type K specific:**
- [ ] Countdown timer starts after card draw
- [ ] Print: 2 cards per A4

**Type M specific:**
- [ ] No right/wrong feedback shown
- [ ] Total interaction ≤5 minutes

---

## Default Slide Structure — Type A

**For two video transcripts (news/current events):**
1. Welcome + key stats | 2. Video 1 + Beobachtungsauftrag | 3. Vocab flip cards (6–8) | 4. MC Video 1 (3–4) | 5. Yes/No (3–4) | 6. Video 2 + Beobachtungsauftrag | 7. MC Video 2 (2–3) | 8. Reflection (2–3 prompts) | 9. Discussion prompts | 10. Results + print + reset

**For single transcript:**
1. Welcome + topic | 2. Video/reading | 3. Vocab flip cards | 4. MC comprehension | 5. Yes/No | 6. Reflection | 7. Discussion | 8. Results + print + reset

Confirm structure with teacher before generating.
