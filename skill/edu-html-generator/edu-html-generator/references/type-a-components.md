# Type A — Interactive Lesson Component Reference

Full implementation patterns for all Type A components.
Claude reads this file when generating a Type A lesson.

**Table of Contents**
1. Vocab Flip Cards ← updated production pattern
2. Multiple Choice
3. Fill-in-the-Blank
4. Yes/No (True/False)
5. Drag & Drop
6. Open Text / Reflection
7. Discussion Prompts
8. Shared Utilities (feedback, score, slide animation)
9. localStorage State System ← new
10. Full Print Export ← new

---

## 1. Vocab Flip Cards

### ⚠️ Production Rules (bugs to avoid)

**Bug 1 — Back face visible on load**: caused by `position:absolute` on both faces.  
**Fix**: Only `.vocab-back` is `position:absolute`. `.vocab-front` stays in normal flow and sets the card height.

**Bug 2 — Cards clipped/truncated**: caused by fixed heights on `.vocab-card-inner`.  
**Fix**: No height on inner container. `.vocab-front` uses `min-height` and grows with content.

**Bug 3 — Unequal card heights across rows**: caused by `auto-fit/minmax` grid.  
**Fix**: Use `repeat(3, 1fr)` fixed columns.

### Data
```js
const VOCABULARY = [
  { word: "Nachhaltigkeit", emoji: "🌱", meaning: "Verantwortungsvoller Umgang mit Ressourcen", hint: "z.B. Recycling, erneuerbare Energie" },
];
const vocabFlipped = {}; // {index: true} — for localStorage persistence
```

### Renderer
```js
function initVocab() {
  const saved = loadState();
  document.getElementById('vocabGrid').innerHTML = VOCABULARY.map((v, i) => `
    <div class="vocab-card${saved.vocabFlipped?.[i] ? ' revealed' : ''}" onclick="flipCard(this,${i})">
      <div class="vocab-card-inner">
        <div class="vocab-front">
          <div class="vocab-emoji">${v.emoji}</div>
          <div class="vocab-word">${v.word}</div>
          <div class="vocab-tap">👆 anklicken</div>
        </div>
        <div class="vocab-back">
          <div class="vocab-emoji">${v.emoji}</div>
          <div class="vocab-word">${v.word}</div>
          <div class="vocab-meaning">${v.meaning}</div>
          ${v.hint ? `<div class="vocab-hint">${v.hint}</div>` : ''}
        </div>
      </div>
    </div>
  `).join('');
}

function flipCard(card, i) {
  if (card.classList.contains('revealed')) return;
  card.classList.add('revealed');
  vocabFlipped[i] = true;
  addScore(1);
  saveState();
}
```

### CSS
```css
/* Fixed 3-column grid — NOT auto-fit/minmax */
.vocab-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 14px; }
@media (max-width: 600px) { .vocab-grid { grid-template-columns: repeat(2, 1fr); } }

.vocab-card { perspective: 800px; cursor: pointer; }
.vocab-card-inner { position: relative; width: 100%; transform-style: preserve-3d; transition: transform 0.55s cubic-bezier(.4,0,.2,1); border-radius: var(--radius-lg); }
.vocab-card.revealed .vocab-card-inner { transform: rotateY(180deg); }

/* FRONT: normal flow, sets card height */
.vocab-front { border-radius: var(--radius-lg); padding: 18px 12px; text-align: center; display: flex; flex-direction: column; align-items: center; justify-content: center; backface-visibility: hidden; -webkit-backface-visibility: hidden; background: var(--bg-card); border: 2px solid transparent; transition: border-color var(--transition); min-height: 150px; }
.vocab-card:hover .vocab-front { border-color: var(--accent); }

/* BACK: absolute overlay — fills same space as front */
.vocab-back { position: absolute; inset: 0; border-radius: var(--radius-lg); padding: 14px 12px; text-align: center; display: flex; flex-direction: column; align-items: center; justify-content: center; backface-visibility: hidden; -webkit-backface-visibility: hidden; background: linear-gradient(135deg, var(--accent), var(--accent-2)); color: white; transform: rotateY(180deg); overflow: hidden; }

.vocab-emoji { font-size: 1.8rem; margin-bottom: 6px; flex-shrink: 0; }
.vocab-word { font-size: 0.95rem; font-weight: 700; margin-bottom: 4px; line-height: 1.3; }
.vocab-tap { font-size: 0.7rem; color: var(--text-secondary); margin-top: 6px; }
.vocab-meaning { font-size: 0.78rem; color: rgba(255,255,255,0.95); line-height: 1.45; }
.vocab-hint { font-size: 0.7rem; color: rgba(255,255,255,0.75); margin-top: 6px; font-style: italic; }
```

---

## 2. Multiple Choice Questions

### ⚠️ Production Rule
Store the **chosen answer index** (not just `true`) so it can be restored from localStorage and exported in the print report.

### Data
```js
const MC_QUESTIONS = [
  { question: "Was bedeutet Nachhaltigkeit?", choices: ["Ressourcen schonen", "Schnell wachsen", "Energie verschwenden"], correct: 0, emoji: "🌍" },
];
const mcAnswered = {}; // {questionIndex: chosenAnswerIndex}
```

### Renderer
```js
function initMC() {
  document.getElementById('mcContainer').innerHTML = MC_QUESTIONS.map((q, qi) => `
    <div class="question-block" id="mc-${qi}">
      <div class="question-text"><span class="q-num">${qi+1}</span> ${q.emoji} ${q.question}</div>
      <div class="choices">
        ${q.choices.map((c, ci) => `
          <button class="choice-btn" onclick="selectMC(${qi},${ci},this)">
            <span class="choice-letter">${String.fromCharCode(65+ci)}</span>${c}
          </button>`).join('')}
      </div>
      <div class="feedback" id="mc-fb-${qi}"></div>
    </div>`).join('');
}

function selectMC(qi, ci, btn) {
  if (mcAnswered[qi] !== undefined) return;
  mcAnswered[qi] = ci; // store index, not true
  const block = document.getElementById(`mc-${qi}`);
  block.querySelectorAll('.choice-btn').forEach(b => b.disabled = true);
  const isCorrect = ci === MC_QUESTIONS[qi].correct;
  btn.classList.add(isCorrect ? 'correct' : 'wrong');
  if (!isCorrect) block.querySelectorAll('.choice-btn')[MC_QUESTIONS[qi].correct].classList.add('correct');
  const fb = document.getElementById(`mc-fb-${qi}`);
  fb.className = `feedback show ${isCorrect ? 'success' : 'error'}`;
  fb.innerHTML = isCorrect ? '✅ Richtig! +2 Punkte' : '❌ Nicht ganz – die richtige Antwort ist markiert.';
  if (isCorrect) addScore(2); else saveState();
}

// Restore MC state from localStorage
function restoreMC(saved) {
  if (!saved.mcAnswered) return;
  Object.entries(saved.mcAnswered).forEach(([qi, ci]) => {
    if (ci === undefined || ci === null) return;
    mcAnswered[qi] = ci;
    const block = document.getElementById(`mc-${qi}`);
    if (!block) return;
    const buttons = block.querySelectorAll('.choice-btn');
    buttons.forEach(b => b.disabled = true);
    buttons[ci]?.classList.add(ci == MC_QUESTIONS[qi].correct ? 'correct' : 'wrong');
    if (ci != MC_QUESTIONS[qi].correct) buttons[MC_QUESTIONS[qi].correct]?.classList.add('correct');
    const fb = document.getElementById(`mc-fb-${qi}`);
    if (fb) {
      fb.className = `feedback show ${ci == MC_QUESTIONS[qi].correct ? 'success' : 'error'}`;
      fb.innerHTML = ci == MC_QUESTIONS[qi].correct ? '✅ Richtig! +2 Punkte' : '❌ Nicht ganz – die richtige Antwort ist markiert.';
    }
  });
}
```

### CSS
```css
.question-block { background: var(--bg-card); border-radius: var(--radius); padding: 20px; margin-bottom: 15px; }
.question-text { font-size: 1.05rem; font-weight: 600; margin-bottom: 14px; display: flex; align-items: flex-start; gap: 10px; line-height: 1.5; }
.q-num { width: 32px; height: 32px; border-radius: 50%; background: var(--accent); color: white; display: flex; align-items: center; justify-content: center; font-size: 0.85rem; font-weight: 700; flex-shrink: 0; }
.choices { display: flex; flex-direction: column; gap: 9px; }
.choice-btn { background: var(--bg-secondary); border: 2px solid var(--border); border-radius: var(--radius); padding: 13px 16px; font-size: 0.95rem; text-align: left; cursor: pointer; color: var(--text-primary); display: flex; align-items: center; gap: 12px; font-family: inherit; transition: all var(--transition); }
.choice-btn:hover:not(:disabled) { border-color: var(--accent); background: color-mix(in srgb, var(--accent) 10%, transparent); }
.choice-letter { width: 28px; height: 28px; border-radius: 50%; background: var(--bg-card); display: flex; align-items: center; justify-content: center; font-weight: 700; font-size: 0.85rem; flex-shrink: 0; }
.choice-btn.correct { border-color: var(--green); background: color-mix(in srgb, var(--green) 15%, transparent); }
.choice-btn.correct .choice-letter { background: var(--green); color: #0f172a; }
.choice-btn.wrong { border-color: var(--red); background: color-mix(in srgb, var(--red) 15%, transparent); }
.choice-btn.wrong .choice-letter { background: var(--red); color: white; }
```

---

## 3. Fill-in-the-Blank

### Data
```js
const FILL_SENTENCES = [
  { sentence: "Die Schweiz produziert jährlich rund ___ Tonnen Abfall.", answer: "6 Millionen" },
];
const WORD_BANK = ["6 Millionen", "recycelt", "Konsumenten", "Energie"];
```

### Renderer
```js
function initFill() {
  document.getElementById('fillContainer').innerHTML = FILL_SENTENCES.map((f, i) => `
    <div class="question-block">
      <div class="fill-sentence">
        <span class="q-num">${i+1}</span>
        ${f.sentence.replace('___', `<input class="fill-input" id="fill-${i}" data-answer="${f.answer}" placeholder="___">`)}
      </div>
    </div>`).join('');
  const shuffled = [...WORD_BANK].sort(() => Math.random() - 0.5);
  document.getElementById('wordBank').innerHTML = shuffled.map(w =>
    `<button class="word-chip" onclick="insertWord('${w}',this)">${w}</button>`).join('');
}
function insertWord(word, chip) {
  const active = document.querySelector('.fill-input:focus') ||
    [...document.querySelectorAll('.fill-input')].find(i => !i.value);
  if (active) { active.value = word; chip.classList.add('used'); }
}
function checkFills() {
  let correct = 0;
  document.querySelectorAll('.fill-input').forEach(inp => {
    const ok = inp.value.trim().toLowerCase() === inp.dataset.answer.toLowerCase();
    inp.classList.toggle('correct', ok);
    inp.classList.toggle('wrong', !ok);
    if (ok) correct++;
  });
  addScore(correct * 2);
  showFeedback('fillFeedback', correct === FILL_SENTENCES.length ? 'success' : 'hint',
    correct === FILL_SENTENCES.length ? '✅ Alle richtig!' : `${correct} von ${FILL_SENTENCES.length} richtig.`);
}
```

### CSS
```css
.fill-sentence { font-size: 1.1rem; line-height: 2.2; display: flex; align-items: center; gap: 8px; flex-wrap: wrap; }
.fill-input { background: var(--bg-primary); border: 2px solid var(--accent); border-radius: 8px; padding: 6px 14px; font-size: 1rem; color: var(--text-primary); font-family: inherit; min-width: 120px; text-align: center; }
.fill-input:focus { outline: none; border-color: var(--accent-2); }
.fill-input.correct { border-color: var(--green); background: color-mix(in srgb, var(--green) 10%, transparent); }
.fill-input.wrong { border-color: var(--red); background: color-mix(in srgb, var(--red) 10%, transparent); }
.word-bank { background: var(--bg-card); border-radius: var(--radius); padding: 16px; margin-top: 15px; }
.word-chips { display: flex; flex-wrap: wrap; gap: 10px; margin-top: 10px; }
.word-chip { background: var(--accent); color: white; border: none; padding: 8px 18px; border-radius: 20px; font-size: 0.95rem; font-weight: 600; cursor: pointer; font-family: inherit; transition: all var(--transition); }
.word-chip:hover { transform: scale(1.05); }
.word-chip.used { opacity: 0.4; pointer-events: none; }
```

---

## 4. Yes/No (True/False)

### ⚠️ Production Rule
Store the **chosen boolean** (`true`/`false`), not just `true`, so state can be restored and printed.

### Data
```js
const YESNO = [
  { statement: "Die Schweiz recycelt mehr als 50% ihres Abfalls.", answer: true, emoji: "♻️" },
];
const ynAnswered = {}; // {index: true|false}
```

### Renderer
```js
function initYesNo() {
  document.getElementById('yesnoContainer').innerHTML = YESNO.map((q, i) => `
    <div class="question-block" id="yn-${i}">
      <div class="question-text" style="flex-direction:column; gap:6px; text-align:center;">
        <span style="font-size:2rem">${q.emoji}</span>
        ${q.statement}
      </div>
      <div class="yesno-btns">
        <button class="yn-btn yes" onclick="answerYN(${i},true,this)">Ja ✓</button>
        <button class="yn-btn no" onclick="answerYN(${i},false,this)">Nein ✗</button>
      </div>
      <div class="feedback" id="yn-fb-${i}"></div>
    </div>`).join('');
}

function answerYN(i, val, btn) {
  if (ynAnswered[i] !== undefined) return;
  ynAnswered[i] = val; // store boolean, not true
  const block = document.getElementById(`yn-${i}`);
  block.querySelectorAll('.yn-btn').forEach(b => b.disabled = true);
  const isCorrect = val === YESNO[i].answer;
  btn.style.outline = `3px solid ${isCorrect ? 'var(--green)' : 'var(--red)'}`;
  const fb = document.getElementById(`yn-fb-${i}`);
  fb.className = `feedback show ${isCorrect ? 'success' : 'error'}`;
  fb.innerHTML = isCorrect ? '✅ Richtig! +1 Punkt' : `❌ Falsch! Die Aussage ist ${YESNO[i].answer ? 'richtig' : 'falsch'}.`;
  if (isCorrect) addScore(1); else saveState();
}

// Restore YN state from localStorage
function restoreYN(saved) {
  if (!saved.ynAnswered) return;
  Object.entries(saved.ynAnswered).forEach(([i, val]) => {
    if (val === undefined || val === null) return;
    ynAnswered[i] = val;
    const block = document.getElementById(`yn-${i}`);
    if (!block) return;
    const btns = block.querySelectorAll('.yn-btn');
    btns.forEach(b => b.disabled = true);
    const isCorrect = val === YESNO[i].answer;
    const chosenBtn = val === true ? btns[0] : btns[1];
    if (chosenBtn) chosenBtn.style.outline = `3px solid ${isCorrect ? 'var(--green)' : 'var(--red)'}`;
    const fb = document.getElementById(`yn-fb-${i}`);
    if (fb) {
      fb.className = `feedback show ${isCorrect ? 'success' : 'error'}`;
      fb.innerHTML = isCorrect ? '✅ Richtig! +1 Punkt' : `❌ Falsch! Die Aussage ist ${YESNO[i].answer ? 'richtig' : 'falsch'}.`;
    }
  });
}
```

### CSS
```css
.yesno-btns { display: flex; gap: 16px; margin-top: 14px; }
.yn-btn { flex: 1; padding: 16px; border: none; border-radius: var(--radius); font-size: 1.1rem; font-weight: 700; cursor: pointer; font-family: inherit; transition: all var(--transition); }
.yn-btn.yes { background: var(--green); color: #0f172a; }
.yn-btn.no { background: var(--red); color: white; }
.yn-btn:hover:not(:disabled) { transform: scale(1.04); }
.yn-btn:disabled { opacity: 0.6; cursor: not-allowed; transform: none; }
```

---

## 5. Drag & Drop (Match items to categories)

### Data
```js
const DRAGDROP = {
  categories: ["Vermeiden", "Verwerten"],
  items: [
    { text: "Mehrwegflasche nutzen", correct: "Vermeiden" },
    { text: "Altglas trennen", correct: "Verwerten" },
  ]
};
```

### Renderer
```js
function initDragDrop() {
  const itemsEl = document.getElementById('ddItems');
  const shuffled = [...DRAGDROP.items].sort(() => Math.random() - 0.5);
  itemsEl.innerHTML = shuffled.map((item, i) =>
    `<div class="dd-item" draggable="true" data-index="${i}" data-correct="${item.correct}"
      ondragstart="dragStart(event)">${item.text}</div>`).join('');
  document.getElementById('ddZones').innerHTML = DRAGDROP.categories.map(cat =>
    `<div class="dd-zone" data-cat="${cat}" ondragover="event.preventDefault()" ondrop="dropItem(event,this)">
      <div class="dd-zone-label">${cat}</div>
      <div class="dd-zone-items"></div>
    </div>`).join('');
}
function dragStart(e) { e.dataTransfer.setData('text', e.target.dataset.index); }
function dropItem(e, zone) {
  e.preventDefault();
  const idx = e.dataTransfer.getData('text');
  const item = document.querySelector(`.dd-item[data-index="${idx}"]`);
  zone.querySelector('.dd-zone-items').appendChild(item);
}
function checkDragDrop() {
  let correct = 0;
  document.querySelectorAll('.dd-item').forEach(item => {
    const zone = item.closest('.dd-zone');
    const ok = zone && zone.dataset.cat === item.dataset.correct;
    item.classList.toggle('correct', ok);
    item.classList.toggle('wrong', !ok);
    if (ok) correct++;
  });
  addScore(correct);
}
```

### CSS
```css
.dd-container { display: grid; grid-template-columns: 1fr 2fr; gap: 20px; }
.dd-items { display: flex; flex-direction: column; gap: 8px; }
.dd-item { background: var(--accent); color: white; padding: 10px 16px; border-radius: 8px; cursor: grab; font-weight: 500; font-size: 0.95rem; user-select: none; transition: all var(--transition); }
.dd-item:active { cursor: grabbing; opacity: 0.8; }
.dd-item.correct { background: var(--green); color: #0f172a; }
.dd-item.wrong { background: var(--red); }
.dd-zones { display: flex; flex-direction: column; gap: 12px; }
.dd-zone { border: 2px dashed var(--border); border-radius: var(--radius); padding: 15px; min-height: 80px; }
.dd-zone-label { font-size: 0.8rem; font-weight: 600; text-transform: uppercase; letter-spacing: 0.06em; color: var(--text-secondary); margin-bottom: 10px; }
.dd-zone-items { display: flex; flex-wrap: wrap; gap: 8px; }
```

---

## 6. Open Text / Reflection

Reflections are auto-saved to localStorage on every `input` event.

### Data
```js
const REFLECTION_PROMPTS = [
  { prompt: "Was hat dich in dieser Lektion am meisten überrascht?", placeholder: "Schreibe hier deine Antwort..." },
];
```

### Renderer
```js
function initReflection() {
  document.getElementById('reflectionContainer').innerHTML = REFLECTION_PROMPTS.map((p, i) => `
    <div class="reflection-block">
      <div class="question-text"><span class="q-num">${i+1}</span>${p.prompt}</div>
      <textarea class="reflection-textarea" id="refl-${i}" placeholder="${p.placeholder}" rows="4"></textarea>
    </div>`).join('');
  // Auto-save on input
  document.querySelectorAll('.reflection-textarea').forEach(ta => {
    ta.addEventListener('input', () => saveState());
  });
}

// Restore from localStorage
function restoreReflections(saved) {
  if (!saved.reflections) return;
  saved.reflections.forEach((val, i) => {
    const el = document.getElementById(`refl-${i}`);
    if (el) el.value = val;
  });
}
```

### CSS
```css
.reflection-block { background: var(--bg-card); border-radius: var(--radius); padding: 20px; margin-bottom: 15px; }
.reflection-textarea { width: 100%; background: var(--bg-secondary); border: 2px solid var(--border); border-radius: var(--radius); padding: 12px; color: var(--text-primary); font-family: inherit; font-size: 0.95rem; resize: vertical; margin-top: 12px; transition: border-color var(--transition); line-height: 1.5; }
.reflection-textarea:focus { outline: none; border-color: var(--accent); }
```

---

## 7. Discussion Prompts (Talk Cards)

### Data
```js
const DISCUSSION = [
  { question: "Welche Massnahme wäre in deinem Betrieb am einfachsten umzusetzen?", starter: "Ich glaube, dass..." },
];
```

### Renderer
```js
function initDiscussion() {
  document.getElementById('discussionContainer').innerHTML = DISCUSSION.map(d => `
    <div class="talk-card">
      <div class="talk-icon">💬</div>
      <div class="talk-question">${d.question}</div>
      <div class="talk-starter"><em>Satzstart: «${d.starter}»</em></div>
    </div>`).join('');
}
```

### CSS
```css
.talk-card { background: color-mix(in srgb, var(--accent) 10%, var(--bg-card)); border: 2px solid var(--accent); border-radius: var(--radius-lg); padding: 20px; margin-bottom: 14px; text-align: center; }
.talk-icon { font-size: 1.8rem; margin-bottom: 8px; }
.talk-question { font-size: 1.05rem; font-weight: 600; margin-bottom: 8px; }
.talk-starter { font-size: 0.85rem; color: var(--text-secondary); }
```

---

## 8. Shared Utilities

### Feedback component
```js
function showFeedback(id, type, msg) {
  const el = document.getElementById(id);
  if (!el) return;
  el.className = `feedback show ${type}`;
  el.innerHTML = msg;
}
```
```css
.feedback { padding: 13px 16px; border-radius: var(--radius); margin-top: 12px; display: none; align-items: center; gap: 10px; font-size: 0.9rem; animation: popIn 0.3s ease; }
.feedback.show { display: flex; }
.feedback.success { background: color-mix(in srgb, var(--green) 15%, transparent); border: 2px solid var(--green); color: var(--green); }
.feedback.error { background: color-mix(in srgb, var(--red) 15%, transparent); border: 2px solid var(--red); color: var(--red); }
.feedback.hint { background: color-mix(in srgb, var(--yellow) 15%, transparent); border: 2px solid var(--yellow); color: var(--yellow); }
@keyframes popIn { from { transform: scale(0.9); opacity: 0; } to { transform: scale(1); opacity: 1; } }
```

### Score system (with localStorage save)
```js
let score = 0;
function addScore(pts) {
  score += pts;
  document.getElementById('scoreDisplay').textContent = score;
  saveState(); // always save after score change
}

function showResults() {
  document.getElementById('finalScore').textContent = score;
  const maxScore = VOCABULARY.length + MC_QUESTIONS.length * 2 + YESNO.length;
  const pct = score / maxScore;
  const stars = pct >= 0.8 ? '⭐⭐⭐' : pct >= 0.5 ? '⭐⭐' : '⭐';
  document.getElementById('finalStars').textContent = stars;
  if (studentName) document.getElementById('resultName').textContent = studentName;
}
```

Results slide HTML must include reset button:
```html
<button onclick="if(confirm('Fortschritt löschen und neu starten?')) clearState()">🔄 Neu starten</button>
```

### Slide animation
```css
.slide { display: none; animation: slideIn 0.35s ease; }
.slide.active { display: block; }
@keyframes slideIn { from { opacity: 0; transform: translateX(20px); } to { opacity: 1; transform: translateX(0); } }
```

### Inline nav-buttons (always hidden — bottom bar handles navigation)
```css
.nav-buttons { display: none; }
```
Keep `<div class="nav-buttons">` in slide HTML for structural clarity, but they are invisible.

---

## 9. localStorage State System

Full implementation for save/restore/clear:

```js
const STORAGE_KEY = 'lesson_[slug]_v1'; // use a unique slug per lesson

function saveState() {
  const s = {
    score,
    studentName,
    currentSlide,
    theme: document.documentElement.getAttribute('data-theme'),
    vocabFlipped: {...vocabFlipped},
    mcAnswered: {...mcAnswered},    // {qi: chosenIndex}
    ynAnswered: {...ynAnswered},    // {i: true|false}
    reflections: REFLECTION_PROMPTS.map((p, i) => document.getElementById(`refl-${i}`)?.value || '')
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

**Restore in DOMContentLoaded — order matters:**
```js
document.addEventListener('DOMContentLoaded', () => {
  // 1. Init all components (renders HTML)
  initVocab(); initMC(); initYesNo(); initReflection(); initDiscussion(); buildProgressDots();

  const s = loadState();

  // 2. Theme
  if (s.theme) {
    document.documentElement.setAttribute('data-theme', s.theme);
    document.getElementById('themeIcon').textContent = s.theme === 'dark' ? '🌙' : '☀️';
  }

  // 3. Student name
  if (s.studentName) {
    studentName = s.studentName;
    document.getElementById('nameModal').style.display = 'none';
    document.querySelectorAll('.student-name').forEach(el => el.textContent = studentName);
  }

  // 4. Score
  if (s.score) {
    score = s.score;
    document.getElementById('scoreDisplay').textContent = score;
  }

  // 5. MC answers (restore visual state)
  restoreMC(s);

  // 6. YN answers
  restoreYN(s);

  // 7. Reflections
  restoreReflections(s);
  // Re-attach auto-save after restoring values
  document.querySelectorAll('.reflection-textarea').forEach(ta => {
    ta.addEventListener('input', () => saveState());
  });

  // 8. Slide — LAST (calls updateProgress which syncs bottom bar)
  goToSlide(s.currentSlide || 1);
});
```

---

## 10. Full Print Export

### ⚠️ Production Rules
- Use `display:none/block` CSS — NOT `body.printing visibility:hidden` (unreliable)
- Include ALL answer types: vocab, MC, YN, reflections + total score
- Color-code by correctness: green border-left = correct, red = wrong, grey = unanswered
- Use `white-space: pre-wrap` for reflection answers
- Print button belongs in the **header** (always visible), not only on results slide

### CSS
```css
@media print {
  body > *:not(#printContent) { display: none !important; }
  #printContent { display: block !important; }
}
#printContent { display: none; }
```

### Full printLesson() function
```js
function printLesson() {
  const now = new Date().toLocaleDateString('de-CH');
  const s = loadState();
  const maxScore = VOCABULARY.length + MC_QUESTIONS.length * 2 + YESNO.length;

  // Vocab section
  const vocabSection = VOCABULARY.map((v, i) => {
    const flipped = s.vocabFlipped?.[i];
    return `<span style="display:inline-block;margin:3px 4px;padding:4px 10px;border-radius:20px;font-size:0.82rem;background:${flipped?'#fef9ec':'#f3f4f6'};color:${flipped?'#92400e':'#9ca3af'};border:1px solid ${flipped?'#f59e0b':'#e5e7eb'};">${flipped?'✅':'⬜'} ${v.word}</span>`;
  }).join('');

  // MC section
  const mcSection = MC_QUESTIONS.map((q, qi) => {
    const ci = s.mcAnswered?.[qi];
    const answered = ci !== undefined && ci !== null;
    const isCorrect = answered && Number(ci) === q.correct;
    const icon = !answered ? '⬜' : isCorrect ? '✅' : '❌';
    const color = !answered ? '#ccc' : isCorrect ? '#4ade80' : '#f87171';
    const bg = !answered ? '#f9f9f9' : isCorrect ? '#f0fdf4' : '#fff5f5';
    return `<div style="margin-bottom:12px;padding:12px 14px;border-radius:4px;border-left:4px solid ${color};background:${bg};page-break-inside:avoid;">
      <p style="font-weight:600;font-size:0.88rem;margin-bottom:5px;">${icon} ${q.emoji} ${q.question}</p>
      <p style="font-size:0.85rem;color:#333;">Antwort: <strong>${answered ? q.choices[ci] : '<em>nicht beantwortet</em>'}</strong></p>
      ${!isCorrect && answered ? `<p style="font-size:0.82rem;color:#666;">Richtig: <em>${q.choices[q.correct]}</em></p>` : ''}
    </div>`;
  }).join('');

  // YN section
  const ynSection = YESNO.map((q, i) => {
    const val = s.ynAnswered?.[i];
    const answered = val !== undefined;
    const isCorrect = answered && val === q.answer;
    const icon = !answered ? '⬜' : isCorrect ? '✅' : '❌';
    const color = !answered ? '#ccc' : isCorrect ? '#4ade80' : '#f87171';
    const bg = !answered ? '#f9f9f9' : isCorrect ? '#f0fdf4' : '#fff5f5';
    return `<div style="margin-bottom:12px;padding:12px 14px;border-radius:4px;border-left:4px solid ${color};background:${bg};page-break-inside:avoid;">
      <p style="font-weight:600;font-size:0.88rem;margin-bottom:5px;">${icon} ${q.emoji} ${q.statement}</p>
      <p style="font-size:0.85rem;color:#333;">Antwort: <strong>${!answered?'<em>nicht beantwortet</em>':val?'Ja':'Nein'}</strong>${!isCorrect&&answered?` → Richtig: <em>${q.answer?'Ja':'Nein'}</em>`:''}</p>
    </div>`;
  }).join('');

  // Reflections
  const reflSection = REFLECTION_PROMPTS.map((p, i) => `
    <div style="margin-bottom:16px;background:#fef9ec;border-left:4px solid #f59e0b;padding:12px 16px;border-radius:4px;page-break-inside:avoid;">
      <p style="font-weight:600;margin-bottom:6px;font-size:0.88rem;">${p.prompt}</p>
      <p style="color:#333;font-size:0.88rem;line-height:1.6;white-space:pre-wrap;">${document.getElementById('refl-'+i)?.value || '<em style="color:#aaa">keine Antwort</em>'}</p>
    </div>`).join('');

  document.getElementById('printContent').innerHTML = `
    <div style="font-family:Georgia,serif;max-width:700px;margin:0 auto;padding:40px 30px;color:#111;">
      <h1 style="font-size:1.5rem;border-bottom:3px solid #f59e0b;padding-bottom:10px;margin-bottom:6px;">[Lesson Title]</h1>
      <p style="color:#555;font-size:0.88rem;margin-bottom:28px;">Schüler/in: <strong>${studentName||'—'}</strong> &nbsp;|&nbsp; Datum: ${now} &nbsp;|&nbsp; Punkte: <strong>${score} / ${maxScore}</strong></p>
      <h2 style="font-size:1rem;color:#b45309;margin-bottom:10px;text-transform:uppercase;">📖 Fachbegriffe</h2>
      <div style="margin-bottom:24px;">${vocabSection}</div>
      <h2 style="font-size:1rem;color:#b45309;margin-bottom:10px;text-transform:uppercase;">❓ Fragen</h2>
      <div style="margin-bottom:24px;">${mcSection}</div>
      <h2 style="font-size:1rem;color:#b45309;margin-bottom:10px;text-transform:uppercase;">🔍 Richtig / Falsch</h2>
      <div style="margin-bottom:24px;">${ynSection}</div>
      <h2 style="font-size:1rem;color:#b45309;margin-bottom:10px;text-transform:uppercase;">✍️ Reflexionen</h2>
      ${reflSection}
      <div style="margin-top:28px;padding:14px 18px;background:#fffbeb;border:2px solid #f59e0b;border-radius:6px;">
        <strong>Gesamtpunkte: ${score} / ${maxScore}</strong>
      </div>
      <p style="font-size:0.78rem;color:#bbb;margin-top:24px;border-top:1px solid #eee;padding-top:10px;">[Lesson subtitle / source info]</p>
    </div>`;
  window.print();
}
```
