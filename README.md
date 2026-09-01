# Study Site

Static study site for ACC coursework — interactive unit pages with objectives,
study guides, flashcards, practice quizzes, discussion prompts and notes.
Built as plain HTML/CSS/JS with no build step, deployed to AWS S3 + CloudFront
via GitHub Actions.

**Live:** https://study.imichaelv.com

---

## Current term — Fall 2026

August 24 – December 13, 2026 (16 weeks)

| Course | Title | Status |
|---|---|---|
| `csis3352/` | CSIS 3352 — Artificial Intelligence | module pages in progress |
| `csis3353/` | CSIS 3353 — Cyber Law & Legal System | module pages in progress |
| `csis3385/` | CSIS 3385 — Database / Web Vulnerability & Security | module pages in progress |

### CSIS 3352 at a glance
- **Instructor:** Dr. Al Amin — al.amin@austincc.edu
- **Meets:** Tuesdays 6:00–8:40 PM lecture, 8:40–9:30 PM lab
- **Location:** Highland Campus, HLC1, Building 1000, Room 2413
- **Weighting:** Quizzes 20% · Assignments 20% · Midterm 25% · Final 35%
- **Midterm:** Oct 20 (covers Modules 1–9) · **Final:** by Dec 13 (cumulative)
- **Last day to withdraw:** Nov 19, 2026

> **Note on course policy:** CSIS 3352 prohibits generative AI for assignments,
> projects, tests and any graded assessment. This repository is a personal
> study tool, not a submission channel — nothing generated here goes into
> graded work.

---

## Repository layout

```
study-site/
├── index.html                     # homepage — active courses + collapsible archive
├── TEMPLATE.md                    # build reference / design system
├── TEMPLATE_unit.html             # unit page skeleton
├── csis3352/                      # Fall 2026 — Artificial Intelligence
├── csis3353/                      # Fall 2026 — Cyber Law & Legal System
├── csis3385/                      # Fall 2026 — Database / Web Vuln & Security
├── archive/
│   └── summer-2026/               # completed courses, still live and browsable
│       ├── hist1302/unit1-4/
│       ├── astr1304/unit1-3/
│       └── govt2306/unit1-5/
├── _versions/                     # old file revisions, not deployed
└── .github/workflows/deploy.yml   # CI/CD
```

**`archive/` vs `_versions/`** — `archive/` holds finished coursework that stays
published and usable. `_versions/` holds superseded drafts of files and is
excluded from deployment.

---

## Adding a unit page

1. Copy `TEMPLATE_unit.html` to `<course>/unit<N>/index.html`.
2. Follow the rules in `TEMPLATE.md` — CSS variables, font names and JS function
   signatures are fixed across the site.
3. Every page needs all six tabs, unit-scoped `localStorage` mastery tracking,
   a reset button, ARIA labels for VoiceOver, and print-active-tab CSS.
4. Add the unit link to the course card in `index.html`.
5. Back-links to the homepage are relative — check the depth is right for where
   the file sits.

## Deploy

```bash
git add .
git commit -m "description of what changed"
git push origin main
```

Push to `main` triggers `aws s3 sync` to `s3://study-imichaelv-com` followed by
a CloudFront invalidation. Live in roughly nine seconds.

`s3 sync` runs with `--delete`, so anything removed from the repo is removed
from the bucket on the next push.
