# Study Site — Build Reference Guide
**Site:** study.imichaelv.com  
**Repo:** github.com/m1chaelv/study-site (private)  
**Deploy:** GitHub Actions CI/CD — live in ~9 seconds after `git push origin main`

---

## Deploy Command
```bash
cd ~/Documents/GitHub/study-site
git add .
git commit -m "description of what changed"
git push origin main
```

---

## Site Structure
```
study.imichaelv.com/
├── index.html                    ← Root landing page (always update this when adding a unit)
├── TEMPLATE.md                   ← This file
├── TEMPLATE_unit.html            ← Blank unit page template
├── hist1302/
│   ├── unit1/index.html          ← COMPLETE
│   ├── unit2/index.html          ← placeholder
│   ├── unit3/index.html          ← placeholder
│   └── unit4/index.html          ← placeholder
├── govt2306/
│   ├── unit1/index.html          ← COMPLETE
│   ├── unit2/index.html          ← placeholder
│   ├── unit3/index.html          ← placeholder
│   ├── unit4/index.html          ← placeholder
│   └── unit5/index.html          ← placeholder
└── astr1304/
    └── unit1/index.html          ← next to build
```

---

## Course Accent Colors
| Course    | Color   | Hex       | CSS rgba for --accent-dim          |
|-----------|---------|-----------|------------------------------------|
| HIST-1302 | Blue    | `#3b82f6` | `rgba(59,130,246,0.12)`            |
| GOVT-2306 | Orange  | `#f97316` | `rgba(249,115,22,0.12)`            |
| ASTR-1304 | Green   | `#22c55e` | `rgba(34,197,94,0.12)`             |
| Fall CSIS | Gray    | `#6b7280` | `rgba(107,114,128,0.12)`           |

---

## Design System (DO NOT CHANGE)
These CSS variables are shared across all pages and must stay identical:

```css
:root {
  --bg: #0a0a0f;
  --surface: #12121a;
  --surface2: #16161f;
  --border: #1e1e2e;
  --text: #e8e8f0;
  --muted: #6b6b80;
  --green: #22c55e;
  --red: #ef4444;
  --yellow: #eab308;
  --purple: #a78bfa;
  /* --accent and --accent-dim change per course */
}
```

Font: `IBM Plex Sans` (body) + `IBM Plex Mono` (labels, badges, code)  
Grid line background: `40px × 40px` using `--accent` at `0.025` opacity

---

## 5-Tab Layout (identical on every unit page)
| Tab ID         | Button Label      | Panel ID             |
|----------------|-------------------|----------------------|
| `overview`     | Objectives        | `panel-overview`     |
| `guide`        | Study Guide       | `panel-guide`        |
| `flashcards`   | Flashcards        | `panel-flashcards`   |
| `quiz`         | Practice Quiz     | `panel-quiz`         |
| `notes`        | Notes Template    | `panel-notes`        |

**Nav button HTML pattern:**
```html
<button class="nav-btn active" onclick="showPanel('overview',this)">📋 Objectives</button>
<button class="nav-btn" onclick="showPanel('guide',this)">📖 Study Guide</button>
<button class="nav-btn" onclick="showPanel('flashcards',this)">🃏 Flashcards</button>
<button class="nav-btn" onclick="showPanel('quiz',this)">✏️ Practice Quiz</button>
<button class="nav-btn" onclick="showPanel('notes',this)">📝 Notes Template</button>
```

---

## JavaScript Function Signatures (DO NOT RENAME)
These must be identical on every unit page:

```javascript
showPanel(id, btn)          // switches active tab
toggleChapter(header)       // opens/closes study guide accordion

renderCard()                // renders current flashcard
flipCard()                  // flips the flashcard
nextCard()                  // advances to next card
prevCard()                  // goes to previous card
shuffleCards()              // randomizes card order
filterCards(ch, btn)        // filters cards by chapter key

setQuizFilter(ch, btn)      // filters quiz by chapter
renderQuiz()                // builds quiz DOM
selectMC(qi, oi)            // records MC answer
storeFITB(qi)               // records fill-in-blank answer
submitQuiz()                // grades and shows results
resetQuiz()                 // clears and re-renders quiz
```

---

## Data Structures

### Flashcard object
```javascript
{ ch: 'ch1', q: 'Question text', a: 'Answer text' }
```
`ch` value must match the filter buttons (e.g. `'ch1'`, `'ch2'`, `'ch17'`, `'ch18'`)

### Quiz question — Multiple Choice
```javascript
{
  ch: 'ch1',
  type: 'mc',
  q: 'Question text',
  opts: ['Option A', 'Option B', 'Option C', 'Option D'],
  ans: 0,        // index of correct answer (0-based)
  exp: 'Explanation shown after submit'
}
```

### Quiz question — Fill in the Blank
```javascript
{
  ch: 'ch1',
  type: 'fitb',
  q: 'The ___ clause allows Congress to expand its authority.',
  ans: ['elastic', 'necessary and proper'],  // array of accepted answers (lowercase)
  display: 'elastic / necessary and proper', // shown if wrong
  exp: 'Explanation shown after submit'
}
```

---

## Objectives Panel (Learning Objectives)

Each objective card uses a chapter color class and supports **expand/collapse** for a full explanation:

```html
<div class="obj-card ch1" onclick="toggleObj(this)">
  <div class="obj-num">1</div>
  <div>
    <div class="obj-text">Short objective statement here.</div>
    <div class="obj-chapter">CH. 1 · LO 1.1</div>
    <div class="obj-detail">
      Full explanation that expands when clicked. Can include
      key terms, examples, and context. Hidden by default.
    </div>
  </div>
</div>
```

CSS for expand behavior:
```css
.obj-detail { display:none; font-size:12px; line-height:1.7; color:#c8c8d8; margin-top:10px; }
.obj-card.expanded .obj-detail { display:block; }
```

JS toggle function:
```javascript
function toggleObj(card) {
  card.classList.toggle('expanded');
}
```

Chapter color classes for `obj-num`:
```css
.ch1 .obj-num  { color:#f97316; background:rgba(249,115,22,0.12); }  /* GOVT Ch.1  */
.ch2 .obj-num  { color:#3b82f6; background:rgba(59,130,246,0.12);  }  /* GOVT Ch.2  */
.ch17 .obj-num { color:#3b82f6; background:rgba(59,130,246,0.12);  }  /* HIST Ch.17 */
.ch18 .obj-num { color:#a78bfa; background:rgba(167,139,250,0.12); }  /* HIST Ch.18 */
.ch19 .obj-num { color:#22c55e; background:rgba(34,197,94,0.12);   }  /* HIST Ch.19 */
.ch20 .obj-num { color:#f97316; background:rgba(249,115,22,0.12);  }  /* HIST Ch.20 */
```

---

## Study Guide Accordion Pattern
```html
<div class="chapter-block">
  <div class="chapter-header" onclick="toggleChapter(this)">
    <div>
      <div class="chapter-title" style="color:var(--accent)">Chapter Title</div>
      <div class="chapter-subtitle">Subtitle / topic list</div>
    </div>
    <span class="chevron">▼</span>
  </div>
  <div class="chapter-body">
    <div class="concept-title">Section Heading</div>
    <div class="concept-body">Content with <strong>bold terms</strong>.</div>
    <span class="key-term">term badge</span>
    <span class="key-term blue">blue badge</span>
    <span class="key-term green">green badge</span>
  </div>
</div>
```

---

## Notes Template Pattern
Two-column grid. Left = Ch.1, Right = Ch.2 (or adjust per unit).
```html
<div class="notes-col-grid">
  <div>
    <div class="notes-col-title">Chapter 1</div>
    <div class="notes-item">Bullet point here</div>
    <div class="notes-item">Another point</div>
  </div>
  <div>
    <div class="notes-col-title">Chapter 2</div>
    <div class="notes-item">Bullet point here</div>
  </div>
</div>
<div class="notes-tip"><strong>Exam tip:</strong> Tip text here.</div>
```

---

## Checklist — Building a New Unit Page

- [ ] Copy `TEMPLATE_unit.html` to `coursecode/unitN/index.html`
- [ ] Update `<title>`, `.unit-badge`, `<h1>`, `.exam-pill`, `.textbook-links`
- [ ] Set `--accent` and `--accent-dim` to the correct course color
- [ ] Set grid background `rgba()` to match accent color at `0.025` opacity
- [ ] Add learning objectives to `panel-overview` with `toggleObj()` expand behavior
- [ ] Fill in `panel-guide` chapter blocks from professor materials
- [ ] Populate `allCards` array with 30–50 flashcards
- [ ] Populate `allQuizData` array with 20–30 questions (mix MC and FITB)
- [ ] Fill in `panel-notes` two-column grid
- [ ] Update `filter-row` buttons to match actual chapter keys used in data
- [ ] Update root `index.html` — activate the new unit link, keep future units dimmed
- [ ] `git add . && git commit -m "..." && git push origin main`

---

## Root index.html — Unit Link Pattern

**Active unit:**
```html
<a href="coursecode/unit1/index.html" class="unit-link">
  <div class="unit-number">Unit 01</div>
  <div class="unit-name">Unit Title Here</div>
  <div class="unit-date">Exam: Mon DD–DD</div>
</a>
```

**Placeholder (future unit):**
```html
<a href="coursecode/unit2/index.html" class="unit-link" style="opacity:0.4;pointer-events:none">
  <div class="unit-number">Unit 02</div>
  <div class="unit-name">Unit Title Here</div>
  <div class="unit-date">Exam: Mon DD–DD</div>
</a>
```

---

## AWS Infrastructure (reference only — already deployed)
| Resource        | Value                                      |
|-----------------|--------------------------------------------|
| S3 Bucket       | `study-imichaelv-com` (private, OAC only)  |
| CloudFront ID   | `E167237RIQ8AB4`                           |
| CloudFront URL  | `d122p2jpgn08zc.cloudfront.net`            |
| ACM Certificate | `arn:aws:acm:us-east-1:626175624238:certificate/e4b5377f-0404-43b2-84af-5d315b53074d` |
| OAC ID          | `E3EIL9LV0Z9GXK`                           |
| IAM Deploy User | `github-deploy` (scoped: S3 write + CloudFront invalidation only) |
| Domain          | `study.imichaelv.com` → Route 53 A alias → CloudFront |
