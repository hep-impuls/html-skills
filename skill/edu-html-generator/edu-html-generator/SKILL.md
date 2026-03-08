---
name: edu-html-generator
description: >
  Generate complete, single-file educational HTML pages for Swiss vocational and language education.
  Use this skill whenever Pietro (or any teacher) wants to create an interactive lesson page, a
  print-first A4 learning document, or a news dossier page from a topic description or transcript.
  Trigger on phrases like: "make me a lesson about X", "create an interactive HTML for X",
  "build a lesson from this transcript", "generate a dossier about X", "create a print worksheet
  for X", "make an interactive lesson", "HTML lesson", "edu page", or any request combining a
  topic/transcript with a classroom or student context. Always use this skill — do not freestyle
  educational HTML without it.
---

# edu-html-generator

Generates production-quality, single-file educational HTML pages in three types:
- **Type A** — Interactive Lesson (slide-based, game-like, dark/light toggle)
- **Type B** — Print-First Document (A4 pages, HKO brochure style)
- **Type C** — Info Dossier (scroll-through, Tailwind CDN, media links)

Always read this file fully before generating. Then follow the Interview → Decide Type → Generate flow.

---

## Step 1: Interview the Teacher

Before writing any code, collect answers to these questions. Ask them all at once in a friendly, conversational way — don't interrogate one by one.

**Required:**
1. **Topic / input** — Free description, transcript, article, or topic name?
2. **Language** — German / English / other?
3. **Audience** — Age, level (A1–C1 / Lehrjahr), school type (ABU, EFL, Gesellschaft, etc.)
4. **Page type** — Interactive lesson / Print document / Info dossier? (or suggest based on input)
5. **Mode** — Self-paced by students / Teacher-led display / Hybrid?

**For Type A (Interactive Lesson) also ask:**
6. **Components** — Which activity types? (see Component Menu below)
7. **Font preference** — Suggest Lexend as default; offer Inter or Nunito as alternatives
8. **Accent color** — Suggest one based on topic, or let teacher choose
9. **Student name modal** — Capture student name at start? (yes/no)
10. **Print button** — Always yes by default

**For Type B (Print Document) also ask:**
6. **Situation color** — red / blue / green / orange / purple (maps to HKO situation A–E)
7. **Number of pages** — Usually 4
8. **Sections needed** — Leitfragen / Mindmap / Handlungsprodukt / Reflexion / etc.

**For Type C (Info Dossier) also ask:**
6. **Media links** — Any SRF/video URLs to embed?
7. **Key facts** — How many fact cards? What categories?
8. **Timeline** — Include a chronology section?

If the teacher provides a transcript or article, extract answers from it before asking.

---

## Step 2: Decide the Page Type

| Signal | Suggest Type |
|--------|-------------|
| "interactive", "quiz", "game", "activity", "self-paced" | A |
| Transcript / news article as input + "lesson" | A |
| "brochure", "print", "ABU", "Lernsituation", "worksheet" | B |
| "dossier", "overview", "news", "Überblick", "timeline" | C |
| Teacher-led classroom display | A or C |

When unclear, suggest Type A and confirm.

---

## Step 3: Generate the HTML

Single file. All CSS and JS embedded. No separate files.

Follow the architecture for the chosen type below.

Read the reference file for detailed component code patterns:
- `references/type-a-components.md` — Interactive lesson components
- `references/type-b-components.md` — Print document components  
- `references/type-c-components.md` — Dossier components

---

## Type A — Interactive Lesson Architecture

### Universal Rules
- Single HTML file, all CSS + JS embedded
- All lesson content in JS DATA constants at the top — never hardcode in HTML
- Renderer functions inject content into DOM on `DOMContentLoaded`
- **Dark/light theme toggle always present** (moon/sun icon in header)
- **🖨️ Ausdruck button always in the header** — always visible, not just on results slide
- **Sticky bottom navigation bar** always present (← Zurück | slide counter | Weiter →)
- **Keyboard navigation**: ← → arrow keys change slides (skip when focus in input/textarea)
- **localStorage persistence**: all state saved after every interaction, fully restored on reload
- Student name modal: if requested, show on load before any content
- Inline `.nav-buttons` in slides are **hidden via CSS** — bottom bar handles all navigation

### HTML Structure
```html
<!DOCTYPE html>
<html lang="[lang]" data-theme="dark">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Lesson Title]</title>
  <link href="https://fonts.googleapis.com/css2?family=Lexend:wght@400;500;600;700&display=swap" rel="stylesheet">
  <style>
    /* 1. CSS Variables (dark + light themes) */
    /* 2. Reset + base */
    /* 3. Layout: sticky header, sticky bottom nav, main-container */
    /* 4. Slide system */
    /* 5. Card system */
    /* 6. Component styles (only what's used) */
    /* 7. Feedback states */
    /* 8. Button system — .nav-buttons { display: none } */
    /* 9. Theme toggle */
    /* 10. Print styles */
    /* 11. Responsive (max-width: 600px) */
  </style>
</head>
<body>
  <!-- Student name modal (if requested) -->
  <!-- Sticky Header: title + 🖨️ Ausdruck + score badge + theme toggle -->
  <!-- Main container: progress bar + dots + slides -->
  <!-- Sticky Bottom Nav: ← Zurück | N / TOTAL | Weiter → -->
  <!-- #printContent div (hidden, shown only at print time) -->
  <script>
    // ==================== DATA ====================
    // ==================== STATE + LOCALSTORAGE ====================
    // ==================== INIT + RESTORE ====================
    // ==================== NAVIGATION ====================
    // ==================== KEYBOARD NAV ====================
    // ==================== SCORING ====================
    // ==================== THEME ====================
    // ==================== PRINT (full export) ====================
    // ==================== [COMPONENT SECTIONS] ====================
  </script>
</body>
</html>
```

### CSS Variable System (both themes required)

```css
:root[data-theme="dark"] {
  --bg-primary: #0f172a;
  --bg-secondary: #1e293b;
  --bg-card: #334155;
  --text-primary: #f1f5f9;
  --text-secondary: #94a3b8;
  --border: #475569;
}
:root[data-theme="light"] {
  --bg-primary: #f8fafc;
  --bg-secondary: #ffffff;
  --bg-card: #f1f5f9;
  --text-primary: #1e293b;
  --text-secondary: #64748b;
  --border: #e2e8f0;
}
:root {
  --accent: [chosen color];
  --accent-2: [second color];
  --green: #4ade80;
  --red: #f87171;
  --yellow: #fbbf24;
  --radius: 12px;
  --radius-lg: 20px;
  --transition: 0.2s ease;
}
```

### Header (with always-visible print button)
```html
<div class="header">
  <div class="header-left">
    <div class="header-title">[emoji] [Title]</div>
    <div class="header-sub">[Subject] · <span class="student-name">Schüler/in</span></div>
  </div>
  <div class="header-right">
    <button class="btn-print-header" onclick="printLesson()">🖨️ Ausdruck</button>
    <div class="score-badge">⭐ <span id="scoreDisplay">0</span> Pts</div>
    <button class="theme-toggle" onclick="toggleTheme()"><span id="themeIcon">🌙</span></button>
  </div>
</div>
```
```css
.header { background: var(--bg-secondary); border-bottom: 1px solid var(--border); padding: 10px 20px; display: flex; align-items: center; justify-content: space-between; position: sticky; top: 0; z-index: 100; }
.header-right { display: flex; align-items: center; gap: 10px; }
.btn-print-header { background: var(--bg-card); border: 1px solid var(--border); border-radius: var(--radius); padding: 6px 14px; font-size: 0.82rem; font-weight: 600; color: var(--text-primary); cursor: pointer; font-family: inherit; transition: all var(--transition); white-space: nowrap; }
.btn-print-header:hover { border-color: var(--accent); color: var(--accent); }
```

### Sticky Bottom Navigation Bar
```html
<!-- Place immediately before </body> -->
<div class="bottom-nav">
  <button class="btn-nav btn-nav-ghost" id="btnPrev" onclick="prevSlide()">← Zurück</button>
  <span class="bottom-nav-center" id="slideCounter">1 / N</span>
  <button class="btn-nav" id="btnNext" onclick="nextSlide()">Weiter →</button>
</div>
```
```css
.bottom-nav { position: fixed; bottom: 0; left: 0; right: 0; background: var(--bg-secondary); border-top: 1px solid var(--border); padding: 10px 24px; display: flex; align-items: center; justify-content: space-between; z-index: 100; }
.bottom-nav-center { font-size: 0.82rem; color: var(--text-secondary); font-weight: 500; }
.btn-nav { background: linear-gradient(135deg, var(--accent), var(--accent-2)); border: none; border-radius: var(--radius); padding: 10px 22px; font-size: 0.9rem; font-weight: 700; color: white; cursor: pointer; font-family: inherit; transition: all var(--transition); }
.btn-nav:hover { transform: translateY(-1px); box-shadow: 0 4px 14px color-mix(in srgb, var(--accent) 35%, transparent); }
.btn-nav:disabled { opacity: 0.35; cursor: not-allowed; transform: none; box-shadow: none; }
.btn-nav-ghost { background: var(--bg-card); color: var(--text-primary); }
/* Suppress inline nav buttons — bottom bar handles navigation */
.nav-buttons { display: none; }
/* Extra padding so last slide content isn't hidden behind bottom bar */
.main-container { max-width: 820px; margin: 0 auto; padding: 28px 20px 90px; }
```

### Keyboard Navigation
```js
document.addEventListener('keydown', e => {
  if (e.target.tagName === 'TEXTAREA' || e.target.tagName === 'INPUT') return;
  if (e.key === 'ArrowRight' || e.key === 'ArrowDown') nextSlide();
  if (e.key === 'ArrowLeft'  || e.key === 'ArrowUp')   prevSlide();
});
```

### Navigation + Progress (syncs bottom bar)
```js
function goToSlide(n) {
  if (n < 1 || n > TOTAL_SLIDES) return;
  document.querySelectorAll('.slide').forEach(s => s.classList.remove('active'));
  document.getElementById(`slide-${n}`).classList.add('active');
  currentSlide = n;
  updateProgress();
  window.scrollTo({ top: 0, behavior: 'smooth' });
  saveState();
  if (n === TOTAL_SLIDES) showResults();
}
function nextSlide() { goToSlide(currentSlide + 1); }
function prevSlide() { goToSlide(currentSlide - 1); }

function updateProgress() {
  const pct = ((currentSlide - 1) / (TOTAL_SLIDES - 1)) * 100;
  document.getElementById('progressFill').style.width = pct + '%';
  document.querySelectorAll('.progress-dot').forEach((dot, i) => {
    dot.classList.toggle('active', i + 1 === currentSlide);
    dot.classList.toggle('completed', i + 1 < currentSlide);
  });
  const counter = document.getElementById('slideCounter');
  if (counter) counter.textContent = `${currentSlide} / ${TOTAL_SLIDES}`;
  const btnPrev = document.getElementById('btnPrev');
  const btnNext = document.getElementById('btnNext');
  if (btnPrev) btnPrev.disabled = currentSlide === 1;
  if (btnNext) btnNext.disabled = currentSlide === TOTAL_SLIDES;
}
```

### localStorage Persistence

Save all student state after every interaction. Restore fully on page load.

**Store actual values, not just flags:**
- MC answers: store chosen **index** (number), not `true`
- YN answers: store chosen **boolean** (`true`/`false`), not `true`
- Vocab: store `{index: true}` per flipped card

```js
const STORAGE_KEY = 'lesson_[slug]_v1';

function saveState() {
  const s = {
    score, studentName, currentSlide,
    theme: document.documentElement.getAttribute('data-theme'),
    vocabFlipped: {...vocabFlipped},
    mcAnswered1: {...mcAnswered1},   // {qi: chosenIndex}
    ynAnswered: {...ynAnswered},      // {i: true|false}
    reflections: REFLECTION_PROMPTS.map((p,i) => document.getElementById(`refl-${i}`)?.value || '')
  };
  try { localStorage.setItem(STORAGE_KEY, JSON.stringify(s)); } catch(e) {}
}

function loadState() {
  try { return JSON.parse(localStorage.getItem(STORAGE_KEY)) || {}; } catch(e) { return {}; }
}

function clearState() {
  try { localStorage.removeItem(STORAGE_KEY); } catch(e) {}
  location.reload();
}
```

**Restore order in DOMContentLoaded:**
1. Theme → `data-theme` + icon
2. Name → hide modal + update `.student-name` spans
3. Score → update display
4. MC answers → re-disable buttons + add correct/wrong classes + show feedback divs
5. YN answers → re-disable buttons + add outline + show feedback divs
6. Vocab → re-add `.revealed` class
7. Reflections → fill textareas
8. Slide → call `goToSlide(s.currentSlide)` last

Add `saveState()` to: `submitName()`, `addScore()`, `toggleTheme()`, and all wrong-answer paths.
Auto-save reflections: `ta.addEventListener('input', () => saveState())` after init.

### Theme Toggle
```js
function toggleTheme() {
  const html = document.documentElement;
  const isDark = html.getAttribute('data-theme') === 'dark';
  html.setAttribute('data-theme', isDark ? 'light' : 'dark');
  document.getElementById('themeIcon').textContent = isDark ? '☀️' : '🌙';
  saveState();
}
```

### Print System

Use `display:none/block` — **NOT** the `body.printing visibility:hidden` pattern (unreliable).

```css
@media print {
  body > *:not(#printContent) { display: none !important; }
  #printContent { display: block !important; }
}
#printContent { display: none; }
```

**Print export must include ALL answer types:**

```js
function printLesson() {
  const now = new Date().toLocaleDateString('de-CH');
  const s = loadState();

  // Vocab section
  const vocabSection = VOCABULARY.map((v, i) => {
    const flipped = s.vocabFlipped?.[i];
    return `<span style="...background:${flipped?'#fef9ec':'#f3f4f6'};...">${flipped?'✅':'⬜'} ${v.word}</span>`;
  }).join('');

  // MC section (for each MC array)
  const mcSection = MC_QUESTIONS.map((q, qi) => {
    const ci = mcAnswered[qi]; // stored index or undefined
    const answered = ci !== undefined && ci !== null;
    const isCorrect = answered && ci === q.correct;
    return `<div style="border-left:4px solid ${!answered?'#ccc':isCorrect?'#4ade80':'#f87171'};...">
      <p>${isCorrect?'✅':answered?'❌':'⬜'} ${q.question}</p>
      <p>Deine Antwort: ${answered ? q.choices[ci] : '<em>nicht beantwortet</em>'}</p>
      ${!isCorrect && answered ? `<p>Richtig: ${q.choices[q.correct]}</p>` : ''}
    </div>`;
  }).join('');

  // YN section
  const ynSection = YESNO.map((q, i) => {
    const val = ynAnswered[i]; // stored boolean or undefined
    const answered = val !== undefined;
    const isCorrect = answered && val === q.answer;
    return `<div style="border-left:4px solid ${!answered?'#ccc':isCorrect?'#4ade80':'#f87171'};...">
      <p>${isCorrect?'✅':answered?'❌':'⬜'} ${q.statement}</p>
      <p>Deine Antwort: ${!answered?'<em>nicht beantwortet</em>':val?'Ja':'Nein'}
        ${!isCorrect&&answered ? ` → Richtig: ${q.answer?'Ja':'Nein'}` : ''}</p>
    </div>`;
  }).join('');

  // Reflections
  const reflSection = REFLECTION_PROMPTS.map((p, i) => `
    <div style="border-left:4px solid #f59e0b; background:#fef9ec; padding:12px 16px; margin-bottom:14px;">
      <p style="font-weight:600;">${p.prompt}</p>
      <p style="white-space:pre-wrap;">${document.getElementById('refl-'+i)?.value || '<em>keine Antwort</em>'}</p>
    </div>`).join('');

  document.getElementById('printContent').innerHTML = `
    <div style="font-family:Georgia,serif; max-width:700px; margin:0 auto; padding:40px 30px; color:#111;">
      <h1>...[title]</h1>
      <p>Schüler/in: ${studentName||'—'} | Datum: ${now} | Punkte: ${score}/${maxScore}</p>
      <h2>📖 Fachbegriffe</h2>${vocabSection}
      <h2>❓ Fragen</h2>${mcSection}
      <h2>🔍 Richtig/Falsch</h2>${ynSection}
      <h2>✍️ Reflexionen</h2>${reflSection}
      <div style="border:2px solid #f59e0b; padding:14px;">Gesamtpunkte: ${score}/${maxScore}</div>
    </div>`;
  window.print();
}
```

### Scoring
```js
let score = 0;
function addScore(pts) {
  score += pts;
  document.getElementById('scoreDisplay').textContent = score;
  saveState();
}
```

Results slide must include: stars, score/maxScore, student name, print button, and:
```html
<button onclick="if(confirm('Fortschritt löschen und neu starten?')) clearState()">🔄 Neu starten</button>
```

---

## Vocab Flip Cards — Production CSS Rules

Two bugs to avoid:
1. **Back face visible on load** — caused by `position:absolute` on both faces
2. **Cards clipped** — caused by fixed heights on inner container

**Correct pattern — front in normal flow, back absolutely overlaid:**

```css
.vocab-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 14px; }
@media (max-width: 600px) { .vocab-grid { grid-template-columns: repeat(2, 1fr); } }
.vocab-card { perspective: 800px; cursor: pointer; }
.vocab-card-inner { position: relative; width: 100%; transform-style: preserve-3d; transition: transform 0.55s cubic-bezier(.4,0,.2,1); border-radius: var(--radius-lg); }
.vocab-card.revealed .vocab-card-inner { transform: rotateY(180deg); }

/* FRONT: in normal flow — sets the card height */
.vocab-front { border-radius: var(--radius-lg); padding: 18px 12px; text-align: center; display: flex; flex-direction: column; align-items: center; justify-content: center; backface-visibility: hidden; -webkit-backface-visibility: hidden; background: var(--bg-card); border: 2px solid transparent; transition: border-color var(--transition); min-height: 150px; }
.vocab-card:hover .vocab-front { border-color: var(--accent); }

/* BACK: absolutely overlays front — fills same space */
.vocab-back { position: absolute; inset: 0; border-radius: var(--radius-lg); padding: 14px 12px; text-align: center; display: flex; flex-direction: column; align-items: center; justify-content: center; backface-visibility: hidden; -webkit-backface-visibility: hidden; background: linear-gradient(135deg, var(--accent), var(--accent-2)); color: white; transform: rotateY(180deg); overflow: hidden; }
```

Front HTML: emoji + word + "👆 anklicken"  
Back HTML: emoji + word + meaning + hint

Use **fixed 3-column grid** (`repeat(3, 1fr)`), NOT `auto-fit/minmax` (causes unequal heights).

---

## Component Menu (Type A)

See `references/type-a-components.md` for full code.

| Component | Data Structure | Notes |
|-----------|---------------|-------|
| **Vocab flip cards** | `[{word, emoji, meaning, hint}]` | Front/back CSS pattern above |
| **Multiple choice** | `[{question, choices[], correct, emoji}]` | Store chosen index |
| **Fill-in-blank** | `[{sentence, answer}]` + word bank | Word chips + input |
| **Yes/No** | `[{statement, answer, emoji}]` | Store chosen boolean |
| **Drag & drop** | `[{items[], targets[]}]` | Match to categories |
| **Open text / reflection** | `[{prompt, placeholder}]` | Auto-saved to localStorage |
| **Discussion prompts** | `[{question, starter}]` | Talk cards |
| **Reading passage** | `{text, highlightedWords[]}` | Hover tooltips |
| **Results slide** | Auto from score | Stars + print + reset |

---

## Type B — Print-First Document Architecture

Read `references/type-b-components.md` for full implementation.

- A4 pages as `.page` divs (width: 210mm, min-height: 297mm)
- `@page { size: A4 portrait; margin: 0; }` in print CSS
- pt/mm units; DM Serif Display + DM Sans + DM Mono
- Situation color via `--sit-color` CSS variable
- Top color bar: `::before` pseudo-element on `.page`
- No theme toggle — always light
- **QR codes (REQUIRED)**: Any URL, embed link, or video the teacher provides MUST become a printed QR code. Use the Video Card + QR pattern from `references/type-b-components.md`. Include `qrcode.js` from cdnjs and generate all codes in a single `DOMContentLoaded` listener.

Situation colors: A→`#C0392B` B→`#2471A3` C→`#1E8449` D→`#D35400` E→`#7D3C98`

---

## Type C — Info Dossier Architecture

Read `references/type-c-components.md` for full implementation.

- Tailwind CSS from CDN acceptable
- Scroll-through layout, sticky header, grid of fact cards
- Dark/light toggle + print button always present

---

## Quality Checklist

- [ ] Content in JS DATA constants, not hardcoded HTML
- [ ] CSS variables for both dark + light themes
- [ ] Theme toggle present + saves to localStorage
- [ ] **🖨️ Ausdruck in header** (always visible)
- [ ] **Sticky bottom nav** with ← counter → and disabled states
- [ ] **← → keyboard navigation** (skips inputs/textareas)
- [ ] **localStorage** saves/restores: score, name, slide, theme, answers, reflections
- [ ] MC answers stored as **index**, YN answers stored as **boolean**
- [ ] Print includes **all answer types** (vocab, MC, YN, reflections, score)
- [ ] Print CSS uses **`display:none/block`** (not `visibility:hidden`)
- [ ] Vocab uses **front-in-flow / back-absolute** CSS pattern
- [ ] Vocab grid uses **`repeat(3, 1fr)`** fixed columns
- [ ] Results slide has **🔄 Neu starten** button
- [ ] `.nav-buttons { display: none }` — bottom bar handles navigation
- [ ] `.main-container` has `padding-bottom: 90px`
- [ ] Immediate feedback on every interaction
- [ ] Responsive at 600px
- [ ] Google Fonts import present
- [ ] Fully self-contained file
- [ ] **[Type B only] QR codes** — every URL/embed/video link provided by teacher becomes a QR code via qrcode.js; `DOMContentLoaded` listener generates all codes; Video Card + QR pattern used

---

## Default Slide Structure

**For two video transcripts (news/current events):**
1. Welcome + key stats
2. Video 1 + Beobachtungsauftrag
3. Vocabulary flip cards (6–8 terms)
4. MC questions Video 1 (3–4)
5. Yes/No true-or-false (3–4)
6. Video 2 + Beobachtungsauftrag
7. MC questions Video 2 (2–3)
8. Reflection / open text (2–3 prompts)
9. Discussion prompts (3–4 talk cards)
10. Results + print + reset

**For single transcript:**
1. Welcome + topic intro
2. Video / reading passage
3. Vocabulary flip cards
4. MC comprehension questions
5. Yes/No statements
6. Reflection open text
7. Discussion prompts
8. Results + print + reset

Confirm structure with teacher before generating.
