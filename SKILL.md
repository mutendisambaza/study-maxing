---
name: study-notes
description: >
  Produces comprehensive, exam-ready study guides and print-ready PDFs from
  course materials (lecture PDFs, PowerPoint slides, past exams, review sheets,
  notes). Use whenever the user wants exam prep material, study notes, a mock
  exam, a cram guide, or a practice paper — files uploaded directly OR via a
  course folder. Handles PDF and PPTX. Outputs: interactive HTML study guide
  (quizzes, SVG diagrams, reveal cards) + cheatsheet + drills + WeasyPrint PDFs
  (study guide + iPad-writable mock exam). Always trigger when the user mentions
  study guides, exam prep, lecture notes, past papers, or "make me something to
  study from."
---

# Study Notes Skill

Outputs:
1. **`study_notes.html`** — interactive study guide: MCQ quizzes per section, SVG diagrams, reveal cards, sidebar TOC, progress bar.
2. **`cheatsheet.html`** — ultra-compact 2-column pre-exam scan material (Space Mono, no explanations, all key rules and formulas).
3. **`drills.html`** — 30+ practice problems with click-to-reveal answers, one per testable concept, aligned to exact exam question format.
4. **`mock_final.html`** — interactive mock exam mirroring real exam structure (point total matches actual exam): sticky score bar, self-mark buttons, model answers.
5. **PDFs via WeasyPrint** — `study_guide.pdf` (all content visible) + `mock_final.pdf` (A4, write spaces).

Read `references/DESIGN_SYSTEM.md` before writing any HTML/CSS.
Read `references/INTERACTIVE_COMPONENTS.md` for component markup and the JS block.
Read `references/PDF_PIPELINE.md` before rendering any PDF.

---

## Content Philosophy

The goal is an A+. Every rule below serves that goal:

- **Cover everything testable.** If a lecture slide covered it, a section covers it. No skipping thin topics.
- **Teach the thinking.** For every concept: what problem it solves, how an expert reasons through it, not just the definition.
- **Quality over volume.** One precise worked example beats three shallow ones. Every sentence earns its place.
- **Past exams are ground truth.** They show what the examiner cares about, at what depth, in what format. Filter everything through them.
- **Self-contained.** Never say "refer to the lecture." If it's needed, it's here.
- **Verify before publishing.** Cross-check every worked example verdict, every answer, every rule against the provided answer keys. A wrong verdict on a worked example is a critical failure — it teaches the student the wrong thing. If no answer key: reason through carefully and flag uncertainty.
- **SVG diagrams where needed.** If a concept is inherently visual (Venn diagrams, logic gates, state machines, flowcharts, argument maps), create SVG diagrams. Do not substitute a text description when a diagram would clarify. Regardless of subject domain.
- **No interactive games.** Do not use flip cards, matching games, or cloze drills. These inflate PDFs and do not mirror exam conditions. Quizzes only — MCQ format aligned with how the real exam asks questions.

---

## Input Modes

**Mode A — Directory:**
```
Courses/<CODE>/
  Lecture Notes/      ← required (.pdf or .pptx)
  Assignments/        ← optional
  Supplemental/       ← courseOutline.pdf, finalExamReview.pdf, midtermReview.pdf
  Past Exams/         ← optional, include marking keys if present
```
Invocation: `/study-notes <CODE> <midterm|final> [--practice-exam]`

**Mode B — File upload:**
Identify each file's role from its content:
- Questions + point values → past exam
- "will/won't be tested" language → review sheet
- Sequential slides with definitions/examples → lecture notes
- Step-by-step solutions with mark annotations → marking key

---

## Workflow

### Step 1 — Discover (Mode A only)

Scan and report inventory: `Found: 5 PPTX lectures, 2 review sheets, 4 past assessments, 1 outline.`

### Step 2 — Scope & Weight

**Scope** = which sections exist. Resolve by priority: review sheet → course outline → lecture filenames.

**Weight** = how deeply each topic is covered. Read all past exams and build:

| Topic | Appears in | Marks | Format |
|---|---|---|---|
| Topic A | 3/3 papers | 20 pts | Proof |
| Topic B | 1/3 papers | 5 pts | MCQ |

- Every paper, high marks → **high-yield**: deep coverage, multiple worked examples, explicit exam strategy.
- Occasional, low marks → **standard**: complete but concise.
- Review sheet only, never tested → **awareness**: definition + one example.

Apply weights before writing a line of HTML. Only convert in-scope lectures.

### Step 3 — Convert Source Files

Run in parallel. PDFs and PPTX both supported.

#### 3-Pre — PPTX → PDF (LibreOffice, preferred)
```bash
libreoffice --headless --convert-to pdf "<file.pptx>" --outdir "Courses/<CODE>/Converted/"
```
Then treat output as a PDF and continue with 3A. If LibreOffice unavailable, use 3A-pptx.

#### 3A-pptx — Direct PPTX extraction (fallback)
```python
from pptx import Presentation
from pathlib import Path

def extract_pptx(pptx_path, output_dir):
    prs = Presentation(pptx_path)
    output_dir = Path(output_dir)
    slides_dir = output_dir / "slides"
    slides_dir.mkdir(parents=True, exist_ok=True)
    parts = []
    for i, slide in enumerate(prs.slides, 1):
        texts = [p.text.strip() for shape in slide.shapes
                 if shape.has_text_frame for p in shape.text_frame.paragraphs if p.text.strip()]
        # Extract embedded images
        for shape in slide.shapes:
            if shape.shape_type == 13:
                img = shape.image
                (slides_dir / f"slide_{i:03d}_img.{img.ext}").write_bytes(img.blob)
        parts.append(f"## Slide {i}\n\n" + "\n".join(texts))
    (output_dir / f"{Path(pptx_path).stem}.md").write_text("\n\n".join(parts))
```
`pip install python-pptx --break-system-packages`

Slides with extracted image shapes are **auto-flagged** for visual rendering regardless of word count.

#### 3A — Markdown extraction (PDF)
```bash
marker_single "<path>" --output_dir "Courses/<CODE>/Converted/" --output_format markdown
```
Fallback:
```python
import pdfplumber
def extract_text(pdf_path, output_path):
    parts = []
    with pdfplumber.open(pdf_path) as pdf:
        for i, page in enumerate(pdf.pages):
            text = page.extract_text()
            if text: parts.append(f"## Slide {i+1}\n\n{text}")
    open(output_path, "w").write("\n\n".join(parts))
```

#### 3B — Flag visual slides
Flag for image rendering if ANY of:
- Fewer than 40 words extracted
- Text contains "Figure", "Diagram", "Architecture", "UML", "model" with little prose
- Multiple short labels, no connecting sentences (class diagrams, flowcharts, state machines)

Render flagged slides only:
```python
import fitz
from pathlib import Path

def render_flagged(pdf_path, output_dir, slide_nums):
    doc = fitz.open(pdf_path)
    Path(output_dir).mkdir(parents=True, exist_ok=True)
    for i in slide_nums:
        pix = doc[i-1].get_pixmap(matrix=fitz.Matrix(2,2))
        pix.save(f"{output_dir}/slide_{i:03d}.png")
```

### Step 4 — Ingest

Read in order. Do not read slide images yet.
1. Review sheets
2. Past exams + marking keys — extract mark-per-step annotations, instructor notes, flagged errors
3. Converted lecture markdown
4. Assignments
5. Course outline

### Step 5 — Build the HTML Study Guide

#### 5.1 — Plan first
- List sections with weights
- Identify diagram candidates (Venn diagrams, flowcharts, argument maps, any concept that is inherently spatial or relational)
- Map worked examples to sections; identify which need verification against answer keys
- Plan model answer section
- Plan cheatsheet blocks (one per major rule/formula group) and drills (one per testable concept)

#### 5.2 — Flag slides to embed
Visual slides from 3B: embed if relationships/flow/structure can't be conveyed in text. Otherwise describe in text.

#### 5.3 — Write the HTML

Structure:
```
[Progress bar + Sidebar TOC]
[Hero]
[Exam Intelligence]
[Table of Contents]
[Content sections]
[Cross-topic connections — final exams only]
[Quick Reference]
[Mock exam — if --practice-exam or user requested]
[Model answers]
```

Use `<!-- SLIDE:lecture/slide_NNN.png:caption -->` placeholders inline with relevant content (never grouped at bottom).

---

### How to Write Each Content Section

Follow this order. Do not skip steps.

**1. Opening (2–4 sentences):** What problem does this concept solve? Why does it matter? Frame it from the perspective of someone using it, not a textbook.

**2. Analogy box:** A real-world comparison that maps the *structure* of the concept — not just the surface. Must earn its place.

**3. Reveal card:** One per defined term. Prompt = the question a student asks themselves. Body = precise definition.

**4. Core rule/mechanism:** The appropriate structured component for this subject — ftable for rule catalogues, step-sequence/derivation table for multi-step reasoning, truth/decision table for boolean logic, code block for programming, SVG diagram for spatial concepts, or a standard `<table>` for any other structured data. Choose based on what the subject demands. Never optional if a rule or procedure exists.

**5. Worked examples (from past papers):**
- High-yield: ≥2 examples. Standard: 1. Awareness: 0.
- Each: source citation, full question as it appeared, step-by-step with reasoning at each step, mark allocation per step (from key; infer proportionally if unavailable).

**6. Warning box:** The specific mistake students make on this topic — named precisely. If past papers show a common wrong-answer pattern, describe it.

**7. Tip box:** How to recognise this question type fast. What to do first under time pressure.

**8. SVG diagram (when concept is visual):** If this section covers a concept that is inherently spatial or relational, build a labelled SVG diagram inline. Do not describe the diagram in text — build it. Adapt to the domain: Venn diagrams for set/logical relations, ER diagrams for databases, state machines for automata/networking, UML class diagrams for OOP, reaction mechanisms for chemistry, circuit diagrams for electronics, probability trees for statistics, argument maps for critical thinking. Use SVG masks and clip-paths for region shading where needed.

**9. MCQ quiz (mandatory, every section):** 3–5 questions.
- Source questions from past exam papers and adjacent/similar question formats first; generate new questions only if coverage is insufficient.
- Match the exact format the real exam uses for this topic — same phrasing style, same level of specificity, same answer format.
- If past exams test fine distinctions, distractors must challenge the same distinctions — not obvious wrong answers.
- One question: definition recall. One: application. One: targets a common misconception.
- Feedback explains *why* correct and *why* each distractor fails. Not just "correct."
- No past exams: calibrate to course outline difficulty level.

---

### Exam Intelligence Section

Include when past exams, review sheet, or outline provide data. Place before content sections.

Spec:
- Marks breakdown table (section, weight, format)
- Topic frequency table (topic, papers it appeared in, avg marks)
- High-yield topics explicitly named with why they're high-yield
- Common exam traps from past marking keys — specific, not generic
- Time allocation guidance per section
- What the examiner rewards — mark-scheme patterns from past keys

Be specific — name the topic, the paper count, the mark value, and the examiner pattern. Example format: *"[Topic X] appeared in N/N papers, averaging M pts. The marking key rewards [specific behaviour] — answers missing [specific element] lost [N] pts per instance."* Generic advice ("study hard") is useless.

---

### Model Answers Section

For every past-exam worked example used, and every marking-key answer that reveals examiner priorities.

Each answer contains:
1. Paper source
2. Full question repeated
3. Step-by-step with reasoning at each step, not just the answer
4. Mark per step (from key; infer proportionally if unavailable)
5. Instructor notes from the key (alternative approaches, common errors flagged)
6. What separates a full-mark answer from 70% — explicitly stated if detectable from the key

---

### Cross-Topic Connections (final exams only)

- Concept dependency map (which topics build on which)
- Past final questions that combined 2+ topics — show them, explain the approach
- Where students apply the wrong technique across topic boundaries

---

### Quick Reference Appendix

For the last 20 minutes before the exam. Not a guide summary — only what a student blanks on under pressure:
- Every formal definition (one line, no explanation)
- Every named rule or procedure the examiner references
- Ordered sequences requiring verbatim recall
- Non-standard notation or formula the course uses
- Terms from marking keys that earn or lose marks

Format: ftable for structured rules, plain list for definitions. Scannable only.

---

#### 5.4 — Embed images
```python
import base64, re
from pathlib import Path

def embed_slides(html_path, converted_dir):
    html = Path(html_path).read_text()
    pattern = re.compile(r'<!-- SLIDE:(.+?)/slide_(\d+)\.png:(.+?) -->')
    def replacer(m):
        img_path = Path(converted_dir) / m.group(1) / "slides" / f"slide_{m.group(2)}.png"
        if not img_path.exists(): return f'<!-- MISSING: {img_path} -->'
        b64 = base64.b64encode(img_path.read_bytes()).decode()
        return (f'<div class="img-block hideable">'
                f'<img src="data:image/png;base64,{b64}" alt="{m.group(3)}">'
                f'<div class="caption">Figure: {m.group(3)}</div>'
                f'<div class="img-overlay" role="button" tabindex="0">'
                f'<div class="img-overlay-title">{m.group(3)}</div>'
                f'<div class="img-overlay-hint">Try to recall this diagram. Click to reveal.</div>'
                f'</div></div>')
    Path(html_path).write_text(pattern.sub(replacer, html))
```

#### 5.5 — Insert JS block
Insert JS from `references/INTERACTIVE_COMPONENTS.md` immediately before `</body>`.

#### 5.6 — Verify
Count and report. If MCQ quizzes = 0, stop and add them. Cross-verify answers before publishing.
- MCQ quizzes (one per content section, ≥3 questions)
- SVG diagrams (one per inherently visual concept)
- Reveal cards (one per defined term)
- Worked example verdicts — all checked against answer key; flag any that could not be verified
- Predict-the-output (every runnable code block with deterministic output)
- **Do not include:** flip card grids, matching games, cloze spans

---

### Step 6 — Render PDFs

See `references/PDF_PIPELINE.md`.

**Study guide PDF:** inject print CSS. All reveal bodies shown, overlays hidden, MCQ correct answers highlighted. No interactive scaffolding.

**Mock exam PDF** (`mock_final.html` → `mock_final.pdf`):
- A4, cover page, section scorecard, write spaces, proof rows, tt grids, scrap page
- Source questions from past papers; mirror real exam section letters + point allocations; add adjacent questions for coverage
- Fallback if no past papers: use course outline exam format

**Interactive mock exam HTML** (`mock_final.html` in browser):
- Sticky score bar, self-mark buttons (✓/✗/partial), contenteditable work areas, click-to-reveal model answers

**Cheatsheet HTML** (`cheatsheet.html`):
- 2-column CSS grid, Space Mono throughout, no prose — tables and compact rule lists only
- One block per major rule/formula group; everything a student needs to scan in the last 20 min before the exam

**Drills HTML** (`drills.html`):
- 30+ practice problems, one per testable concept; click-to-reveal answers
- Questions written in the same format the real exam uses — not abstract exercises
- Source from past exams first, generate adjacent questions for gaps

### Step 7 — Deliver

1. `study_guide.pdf` — print-ready study guide
2. `mock_final.pdf` — print-ready mock exam
3. `study_notes.html` — interactive study guide
4. `mock_final.html` — interactive mock exam
5. `cheatsheet.html` — ultra-compact pre-exam scan sheet
6. `drills.html` — click-to-reveal practice problems

---

## Non-Negotiables

- No `<pre>` for structured or tabular data — use `<table>` with design system classes. Pre blocks are only for raw code output.
- Cite every worked example with `.paper-src`.
- PDFs show everything — no hidden content, no interactive states.
- All colours from design system only.
- HTML fully self-contained (base64 images, inline CSS/JS — Google Fonts only network call).
- Every section complete enough that a student reading only that section can answer an exam question on that topic.
