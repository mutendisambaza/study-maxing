# study-maxing

AI Skill that produces A+ yielding study material to aid in prep for assessments and exams

> **Pro tip:** If you have lecture slides, run them through an AI first and have it extract the content as `.md` files — the skill will get significantly better results from clean markdown than raw PDFs or PowerPoints.

---

## What it does

Feed it your lectures, past exams, and review sheets — it reads everything, figures out what the examiner actually tests, and outputs a full study kit: interactive guides, a mock exam, a cheatsheet, practice drills, and print-ready PDFs. All in one shot.

---

## What you give it

| Input | Required |
|---|---|
| Lecture slides (`.pdf`, `.pptx`, or `.md`) | Yes |
| Past exams + marking keys | Strongly recommended |
| Review / exam prep sheets | Strongly recommended |
| Course outline | Optional |
| Assignments | Optional |

---

## What you get back

| Output | What it is |
|---|---|
| `study_notes.html` | Interactive guide — MCQ quizzes, SVG diagrams, reveal cards, sidebar TOC |
| `cheatsheet.html` | Ultra-compact 2-column scan sheet for the last 20 min before the exam |
| `drills.html` | 30+ click-to-reveal practice problems, one per testable concept |
| `mock_final.html` | Interactive mock exam with self-marking and model answers |
| `study_guide.pdf` | Print-ready study guide (all content visible) |
| `mock_final.pdf` | Print-ready A4 mock exam with write spaces |

---

## Setup

### 1. Install the skill

Copy `SKILL.md` into your Claude Code project's `.claude/skills/` directory, or point your skill source at this repo.

### 2. Install dependencies

```bash
pip install weasyprint marker-pdf pdfplumber pymupdf python-pptx --break-system-packages
```

LibreOffice handles `.pptx` → PDF conversion (preferred):
```bash
brew install --cask libreoffice   # macOS
sudo apt install libreoffice      # Linux
```

---

## Usage

**Option A — Course folder:**

Organize your files like this:
```
Courses/MATH101/
  Lecture Notes/     ← .pdf, .pptx, or .md slides
  Past Exams/        ← exams + marking keys
  Supplemental/      ← review sheets, course outline
  Assignments/       ← optional
```

Then invoke:
```
/study-notes MATH101 final
/study-notes MATH101 midterm --practice-exam
```

**Option B — Just upload files:**

Drop your files directly into the conversation and ask:
```
Make me a study guide from these. Final exam is next week.
```

The skill identifies what each file is from its content and proceeds automatically.

---

## What the outputs look like

- **Interactive HTML guide** — opens in any browser, no server needed. Quizzes are clickable, cards are revealable, progress tracked in the sidebar.
- **Mock exam HTML** — sticky score bar and self-mark buttons to simulate exam conditions.
- **PDFs** — fully static, everything visible, safe to print.
- **Cheatsheet** — two columns of rules and formulas only, no prose, built for scanning.

---

## Tips

- Past exam papers are the most valuable input. The skill weights topics by how often and how heavily they're tested, and writes worked examples directly from real papers.
- Marking keys unlock the most useful output — the skill extracts exactly what the examiner rewards and what common mistakes cost marks.
- Use `--practice-exam` to include the mock exam in your output bundle.
