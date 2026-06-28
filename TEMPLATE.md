# Study Site — Build Reference Guide
**Site:** study.imichaelv.com  
**Repo:** github.com/m1chaelv/study-site (private)  
**Deploy:** GitHub Actions CI/CD — live in ~9 seconds after `git push origin main`

---

## SESSION STARTER PROMPT
Save this in Apple Notes. Paste at the top of every new Claude session:

```
I'm building a study unit page for study.imichaelv.com.

RULES — follow exactly:
1. Match the design system in TEMPLATE.md precisely — no changes to CSS variables, font names, or JS function signatures
2. Source material ONLY — base all content on the provided files. Flag conflicts with general knowledge but keep textbook version as the answer
3. No content appears unless it comes from provided syllabus, study guide, lecture, or textbook material
4. Output: one complete index.html ready to drop into the correct folder, zero edits needed
5. Include all 6 tabs: Objectives, Study Guide, Flashcards, Practice Quiz, Discussion, Notes
6. Include mastery tracking (localStorage, unit-scoped), reset button, ARIA labels for iPhone VoiceOver, and print-active-tab CSS
7. Flashcard and quiz counts are uncapped — generate as many as the material supports
8. Discussion scoring: checklist points generated from learning objectives in the provided materials

[PASTE FULL TEMPLATE.md CONTENTS HERE]

Build: [COURSE CODE] [Unit/Module N] — [Title]
Chapters: [list]
Accent: [hex from color table]

[UPLOAD: syllabus, study guide, PPT notes, chapter quiz questions, lecture transcripts]
```

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
├── index.html
├── TEMPLATE.md
├── TEMPLATE_unit.html
├── hist1302/
│   ├── unit1/index.html    ← COMPLETE (exam done Jun 20)
│   ├── unit2/index.html    ← placeholder
│   ├── unit3/index.html    ← placeholder
│   └── unit4/index.html    ← placeholder
├── govt2306/
│   ├── unit1/index.html    ← COMPLETE (exam done Jun 19-21)
│   ├── unit2/index.html    ← placeholder
│   ├── unit3/index.html    ← placeholder
│   ├── unit4/index.html    ← placeholder
│   └── unit5/index.html    ← placeholder
└── astr1304/
    ├── unit1/index.html    ← COMPLETE (exam done Jun 22-27)
    ├── unit2/index.html    ← NEXT: Ch.7-10, exam Jul 5-9
    └── unit3/index.html    ← placeholder
```

---

## Course Accent Colors
| Course    | Hex       | --accent-dim                    |
|-----------|-----------|---------------------------------|
| HIST-1302 | `#3b82f6` | `rgba(59,130,246,0.12)`         |
| GOVT-2306 | `#f97316` | `rgba(249,115,22,0.12)`         |
| ASTR-1304 | `#22c55e` | `rgba(34,197,94,0.12)`          |
| Fall CSIS | `#6b7280` | `rgba(107,114,128,0.12)`        |

---

## Design System (DO NOT CHANGE)
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
  --orange: #f97316;
  /* --accent and --accent-dim set per course */
}
```
Font: `IBM Plex Sans` (body) · `IBM Plex Mono` (labels/badges/code)  
Grid: 40×40px using `--accent` at `0.025` opacity

---

## 6-Tab Layout
| Tab ID       | Label          | Panel ID           |
|--------------|----------------|--------------------|
| `overview`   | Objectives     | `panel-overview`   |
| `guide`      | Study Guide    | `panel-guide`      |
| `flashcards` | Flashcards     | `panel-flashcards` |
| `quiz`       | Practice Quiz  | `panel-quiz`       |
| `discussion` | Discussion     | `panel-discussion` |
| `notes`      | Notes Template | `panel-notes`      |

Nav pattern (with ARIA):
```html
<nav class="topnav" role="tablist" aria-label="Study sections">
  <button class="nav-btn active" onclick="showPanel('overview',this)" role="tab" aria-selected="true" aria-controls="panel-overview">📋 Objectives</button>
  <button class="nav-btn" onclick="showPanel('guide',this)" role="tab" aria-selected="false" aria-controls="panel-guide">📖 Study Guide</button>
  <button class="nav-btn" onclick="showPanel('flashcards',this)" role="tab" aria-selected="false" aria-controls="panel-flashcards">🃏 Flashcards</button>
  <button class="nav-btn" onclick="showPanel('quiz',this)" role="tab" aria-selected="false" aria-controls="panel-quiz">✏️ Practice Quiz</button>
  <button class="nav-btn" onclick="showPanel('discussion',this)" role="tab" aria-selected="false" aria-controls="panel-discussion">💬 Discussion</button>
  <button class="nav-btn" onclick="showPanel('notes',this)" role="tab" aria-selected="false" aria-controls="panel-notes">📝 Notes Template</button>
</nav>
```

---

## JS Function Signatures (DO NOT RENAME)
```javascript
showPanel(id, btn)           // tab switch + aria-selected update
toggleChapter(header)        // study guide accordion
toggleObj(card)              // objective expand/collapse
toggleObjMastery(btn, key)   // objective mastery checkbox

renderCard()                 // render current flashcard
flipCard()                   // flip flashcard
nextCard()                   // next card
prevCard()                   // previous card
shuffleCards()               // shuffle deck
filterCards(ch, btn)         // filter by chapter
toggleMastered(idx)          // mark/unmark flashcard mastered

setQuizFilter(ch, btn)       // filter quiz by chapter
renderQuiz()                 // build quiz DOM
selectMC(qi, oi)             // MC answer selection
storeFITB(qi)                // FITB answer storage
submitQuiz()                 // grade quiz
resetQuiz()                  // reset quiz

renderDiscussion()           // render all disc questions from DISC_QUESTIONS
submitDisc(idx)              // lock textarea, reveal rubric
resetDisc(idx)               // reset a disc question
updateDiscScore(idx)         // recompute self-grade score
toggleModel(idx)             // show/hide model answer
filterDisc(ch, btn)          // filter disc by chapter

resetAllMastery()            // clear unit mastery from localStorage
getMasteryKey()              // returns unit-scoped prefix string
```

---

## Data Structures

### Flashcard
```javascript
{ ch: 'ch1', q: 'Question', a: 'Answer' }
```

### Quiz — MC
```javascript
{ ch:'ch1', type:'mc', q:'Question', opts:['A','B','C','D'], ans:0, exp:'Explanation' }
```

### Quiz — FITB
```javascript
{ ch:'ch1', type:'fitb', q:'The ___ ...', ans:['answer','alt'], display:'Answer', exp:'Explanation' }
```

### Discussion question
```javascript
{
  ch: 'ch1',
  q: 'Full question prompt.',
  points: [
    { text: '<strong>Point 1</strong> — description' },
    { text: '<strong>Point 2</strong> — description' },
  ],
  model: 'Full model answer paragraph.'
}
```
Points drawn from learning objectives. Each = 1 self-grade checkbox. Score = checked/total × 100.

---

## Mastery Tracking
localStorage key prefix: `mastery_[coursecode]_[unitN]_`  
Per flashcard: `mastery_hist1302_unit1_0` = `'1'` (mastered) or absent.  
Per objective: `masteryobj_hist1302_unit1_1` = `'1'` or absent.  
Reset button clears only keys starting with this unit's prefix.

---

## Print CSS (active tab only)
```css
@media print {
  body::before, .topnav, .exam-pill, .textbook-links,
  footer, .fc-controls, .fc-nav, .filter-row,
  #submit-btn, #quiz-score, .disc-btn-row, .disc-rubric,
  .obj-master-btn, .reset-mastery-btn, .mastery-bar { display: none !important; }
  .panel { display: none !important; }
  .panel.active { display: block !important; }
  *, *::before, *::after {
    background: white !important; background-color: white !important;
    background-image: none !important; color: black !important;
    border-color: #bbb !important; box-shadow: none !important;
    -webkit-text-fill-color: black !important;
  }
  body { font-size: 12pt; }
  .chapter-body { display: block !important; }
  @page { margin: 0.75in; }
}
```

---

## ARIA Requirements
- `<html lang="en">`
- Nav: `role="tablist"`, buttons: `role="tab"` + `aria-selected` + `aria-controls`
- Panels: `role="tabpanel"` + `aria-labelledby`
- Flashcard Q/A divs: `aria-live="polite"` so VoiceOver reads updates
- Icon-only buttons: `aria-label` describing action
- Accordions: `aria-expanded` toggled by JS
- Obj cards: `role="button"` + `tabindex="0"` + `aria-expanded`

---

## Chapter Color Classes (obj-num badges)
```css
/* HIST-1302 */
.ch17 .obj-num { color:#3b82f6; background:rgba(59,130,246,0.12); }
.ch18 .obj-num { color:#a78bfa; background:rgba(167,139,250,0.12); }
.ch19 .obj-num { color:#22c55e; background:rgba(34,197,94,0.12); }
.ch20 .obj-num { color:#f97316; background:rgba(249,115,22,0.12); }
/* GOVT-2306 */
.ch1  .obj-num { color:#f97316; background:rgba(249,115,22,0.12); }
.ch2  .obj-num { color:#3b82f6; background:rgba(59,130,246,0.12); }
/* ASTR-1304 — use --accent (green) for all chapters */
```

---

## New Unit Build Checklist
- [ ] Copy `TEMPLATE_unit.html` → `coursecode/unitN/index.html`
- [ ] Set title, unit-badge, h1, exam-pill, textbook-links
- [ ] Set `--accent` and `--accent-dim`
- [ ] Update `getMasteryKey()` return value
- [ ] Populate objectives with mastery buttons
- [ ] Fill study guide accordions
- [ ] Populate `allCards` (uncapped)
- [ ] Populate `allQuizData` (uncapped, mix MC + FITB)
- [ ] Populate `DISC_QUESTIONS` from learning objectives
- [ ] Fill notes two-column grid
- [ ] Update all filter-row buttons to match chapter keys
- [ ] Update root `index.html` (activate link, dim future units)
- [ ] `git add . && git commit -m "feat: [course] unit [N]" && git push origin main`

---

## AWS Reference
| Resource     | Value                              |
|--------------|------------------------------------|
| S3 Bucket    | `study-imichaelv-com`              |
| CloudFront   | `E167237RIQ8AB4`                   |
| IAM User     | `github-deploy`                    |
| Domain       | `study.imichaelv.com`              |
