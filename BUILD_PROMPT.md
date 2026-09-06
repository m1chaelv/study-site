# Study Site — Session Starter for New Modules

Save this file in the repo root next to `TEMPLATE.md`. Paste the **prompt block** below
at the top of a new Claude session, attach the **reference files**, and add the
**build line**. Works for any course: CSIS-3352, CSIS-3353, CSIS-3385, or a future one.

---

## 1. Files to attach every session

| # | File | Why it's needed |
|---|------|-----------------|
| 1 | `TEMPLATE.md` | Design system, JS signatures, data structures, ARIA and print rules |
| 2 | `TEMPLATE_unit.html` | The reusable skeleton — CSS and engine functions |
| 3 | `csis3352/module01/index.html` | **A finished page built under the current structure.** More useful than the template alone — shows real density, flag-note usage, and the scoped-filter fix. Replace with a newer finished page once one exists for that course. |
| 4 | The course syllabus PDF | Exam dates, module list, grading weights, quiz/assignment due dates |
| 5 | The lecture deck(s) for the module being built | **The only source of content.** Nothing goes on the page that isn't in here. |
| 6 | `index.html` (repo root) | The homepage. Include it so the session hands back the complete updated file with the new module link already in place — no hand-splicing. |

Optional but helpful when they exist: study guides, chapter quiz banks, lab
instructions, lecture transcripts, and any posted list of learning objectives.

**Attach the whole repo instead?** Don't. These six are enough context and keep the
session fast. Always send the *current* index.html — it changes every time a module
is added, and a stale copy means the returned homepage silently drops other links.

---

## 2. Prompt block — paste this at the top

```
I'm building a study module page for study.imichaelv.com. Attached: TEMPLATE.md,
TEMPLATE_unit.html, a finished reference page, the course syllabus, the lecture
deck(s) for this module, and the current root index.html.

RULES — follow exactly:

CONTENT
1. Source material ONLY. Every fact on the page must come from the attached deck,
   syllabus, or study material. No outside knowledge.
2. If the deck names a term but never defines it, do NOT define it from general
   knowledge — add a yellow .flag-note saying the definition is absent from the
   source. Same for internal contradictions in the deck.
3. If the deck has no stated learning objectives, derive them from its section
   structure and say so in a flag-note at the top of the Objectives tab.
4. Flashcard and quiz counts are uncapped — generate as many as the material
   supports. Aim for the density of the reference page.
5. Reproduce the deck's comparison tables as real HTML tables in the Study Guide.

STRUCTURE
6. Match TEMPLATE.md exactly — no changes to CSS variables, font names, or JS
   function signatures.
7. All 6 tabs: Objectives, Study Guide, Flashcards, Practice Quiz, Discussion,
   Notes Template.
8. Objectives and DISC_QUESTIONS are 1:1 and in the same order. Each objective's
   .obj-body bullets ARE that discussion question's rubric points — author once,
   use twice.
9. Chapter keys: pick 2–3 that match how the material actually splits, and use the
   same keys in obj-card classes, all three filter rows, allCards, allQuizData,
   and DISC_QUESTIONS.
10. Keep the scoped-filter fix from the reference page: filterCards, setQuizFilter,
    and filterDisc must call activateFilter(btn), which scopes highlighting to
    btn.closest('.filter-row'). The original template cleared all three panels.
11. Mastery keys — flashcards: mastery_[course]_module[NN]_ · objectives:
    obj_[course]_module[NN]_[n]. Must be unique per module.
12. Back-links to the homepage: ../../index.html, in both the header and the footer.
13. ARIA per TEMPLATE.md, plus keyboard (Enter/Space) handling on .obj-card.

OUTPUT
14. One complete index.html, ready to drop in with zero edits.
15. Also return the COMPLETE updated root index.html with this module's link
    activated — not a snippet. I copy whole files, not fragments.
16. Validate before handing it over: JS parses, every MC ans index is in range,
    every FITB has a blank and accepted answers, no duplicate flashcard questions,
    mastery keys unique, obj-card count == DISC_QUESTIONS count.
17. Deliver as FILES I copy in — not a git patch.

Build: [COURSE CODE] Module [NN] — [TITLE]
Path: [coursecode]/module[NN]/index.html
Accent: [hex + rgba from the table below]
```

---

## 3. Accent colors

| Course | `--accent` | `--accent-dim` |
|---|---|---|
| CSIS-3352 Artificial Intelligence | `#a855f7` | `rgba(168,85,247,0.12)` |
| CSIS-3353 Cyber Law | `#06b6d4` | `rgba(6,182,212,0.12)` |
| CSIS-3385 Database / Web Vulnerability | `#f43f5e` | `rgba(244,63,94,0.12)` |

The grid background in `body{}` is hardcoded rgba — swap it to the accent too, in
both `repeating-linear-gradient` rules, at `0.025` alpha.

---

## 4. Repo layout for new modules

```
csis3352/module01/index.html    ← built
csis3352/module02/index.html    ← next
csis3353/module01/index.html
csis3385/module01/index.html
```

Two levels deep, so `../../index.html` reaches the homepage. Delete the folder's
`.gitkeep` when you add the first real page.

---

## 5. Activating the link on the homepage

Each Fall course card currently ends with:

```html
<div class="coming-soon">Module pages in progress</div>
```

Replace that with a unit grid (drop the `coming-soon` div entirely once a course
has at least one module):

```html
<div class="unit-grid">
  <a href="csis3353/module01/index.html" class="unit-link" style="border-color:rgba(6,182,212,0.3);">
    <div class="unit-number">Module 01</div>
    <div class="unit-name">[module title]</div>
    <div class="unit-date">[due date]</div>
  </a>
</div>
```

Use that course's accent at `0.3` alpha for the border. Add one `<a>` per module.

---

## 6. Install and deploy

```bash
cd ~/Documents/GitHub/study-site
mkdir -p [coursecode]/module[NN]
# copy the new index.html into it
# apply the homepage snippet to index.html
rm -f [coursecode]/.gitkeep          # only on that course's first module

open [coursecode]/module[NN]/index.html
```

Check in the browser before committing: all six tabs, flip a flashcard, submit the
quiz empty (everything red with explanations), mark an objective mastered and
confirm the bar moves, reload and confirm it persisted, then Cmd+P to confirm only
the active tab prints.

```bash
git add -A
git commit -m "feat: [course] module [NN] — [title]"
git push origin main
```

Live in about nine seconds. Hard-reload with Cmd+Shift+R.

---

## 7. macOS / zsh gotchas

- `#` is not a comment in interactive zsh. Run `setopt interactive_comments` once
  per session before pasting any commented block.
- BSD `sed` needs an empty argument: `sed -i '' 's/old/new/g' file`. Plain `sed -i`
  fails.
- `*.patch` is gitignored. Take files, not patches — a patch only applies to a tree
  that hasn't been hand-edited, which is rarely the case here.
- `s3 sync` runs with `--delete`, so anything removed from the repo disappears from
  the bucket on the next push.

---

## 8. Course policy note

CSIS 3352 prohibits generative AI for assignments, projects, tests, and any graded
assessment. This site is a personal study tool built from posted lecture material —
it is not a submission channel. Check each course's own syllabus before assuming
the same boundary applies; CSIS-3353 and CSIS-3385 may word their policies
differently.
