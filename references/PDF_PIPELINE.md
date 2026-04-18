# PDF Pipeline Reference

Always use WeasyPrint. Never canvas-based, Puppeteer, or image-based approaches.

```bash
pip install weasyprint --break-system-packages
```

Suppress font warnings in every script:
```python
import warnings
warnings.filterwarnings('ignore')
```

---

## Output 1 — Study Guide PDF

Inject print CSS from DESIGN_SYSTEM.md into the static HTML, then render.

```python
import weasyprint, warnings
warnings.filterwarnings('ignore')

with open('study_notes.html', 'r') as f:
    content = f.read()

# Print CSS is defined in DESIGN_SYSTEM.md — paste it here as a <style> block
print_css = """<style>
@page { size: A4; margin: 12mm 14mm 16mm 14mm; }
@page :first { margin-top: 8mm; }
body { max-width: 100% !important; }
.container { max-width: 100% !important; padding: 0 !important; }
.nav-strip, .sidebar-toc, .reading-progress { display: none !important; }
.reveal-body { display: block !important; }
.reveal-hint { display: none !important; }
.img-overlay { display: none !important; }
.img-block.hideable img { filter: none !important; }
.cloze { background: white !important; color: #c8304a !important; border-bottom: 1.5px solid #c8304a; padding: 0 4px; }
.quiz-opt.correct { background: rgba(13,146,100,0.08) !important; }
.quiz-feedback { display: block !important; }
.flipcard { height: auto; perspective: none; }
.flipcard-inner { transform: none !important; }
.flipcard-front, .flipcard-back { position: static; backface-visibility: visible; transform: none !important; margin-bottom: 8px; }
.predict .predict-output { display: block !important; }
.predict .predict-btn { display: none; }
.match-tile { background: white !important; }
.section-block { break-inside: avoid-page; margin-bottom: 12pt; }
.section-header { break-after: avoid; }
.card, .analogy, .warn, .tip, .ftable, .proof-table, .vsteps, .tt-wrap { break-inside: avoid; }
body { font-size: 9pt !important; }
p, li { font-size: 8.5pt !important; }
@page { @bottom-center { content: counter(page); font-family: 'Space Mono', monospace; font-size: 7pt; color: #7a7670; } @bottom-right { content: "Study Guide"; font-family: 'Space Mono', monospace; font-size: 6pt; color: #d8d4cc; text-transform: uppercase; letter-spacing: 0.08em; } }
</style>"""

content = content.replace('</head>', print_css + '</head>')

with open('/tmp/study_guide_for_pdf.html', 'w') as f:
    f.write(content)

html = weasyprint.HTML(filename='/tmp/study_guide_for_pdf.html')
doc = html.render()
print(f'Study guide: {len(doc.pages)} pages')
doc.write_pdf('study_guide.pdf')
```

---

## Output 2 — Mock Exam PDF (`mock_final.html` → `mock_final.pdf`)

A separate HTML file built from scratch. A4, iPad-writable, with write spaces. Point total and section structure mirror the actual exam for this course.

### Page setup

```css
@page {
  size: A4; margin: 14mm 16mm 16mm 16mm;
}
@page :first { margin-top: 10mm; }
@page {
  @bottom-center { content: counter(page); font-family: 'Space Mono', monospace; font-size: 7pt; color: #7a7670; }
  @bottom-right { content: "Mock Exam"; font-family: 'Space Mono', monospace; font-size: 6pt; color: #d8d4cc; text-transform: uppercase; letter-spacing: 0.08em; }
}
```

### Cover page

```html
<div class="cover">
  <div class="cover-tag">COURSE CODE · Subject</div>
  <h1>Topic<br><span>Mock Exam</span></h1>
  <div class="cover-sub">Practice Paper · All Question Types · iPad Edition</div>
  <div class="cover-rule"></div>
  <div class="cover-info">
    <div class="cover-field"><span class="cover-field-label">Name</span><div class="cover-field-line"></div></div>
    <div class="cover-field"><span class="cover-field-label">Student #</span><div class="cover-field-line"></div></div>
    <div class="cover-field"><span class="cover-field-label">Date</span><div class="cover-field-line"></div></div>
  </div>
  <div class="cover-sections">
    <div class="cover-sections-header">Exam Sections</div>
    <div class="cover-section-row">
      <span class="csr-letter">A</span>
      <span class="csr-title">Section title</span>
      <span class="csr-pts">N pts each × N</span>
      <span class="csr-score">______ / N</span>
    </div>
    <!-- repeat per section, then a TOTAL row -->
  </div>
</div>
```

```css
.cover { page-break-after: always; min-height: 270mm; display: flex; flex-direction: column; align-items: center; justify-content: center; background: linear-gradient(150deg, #edeaf4 0%, #f0edf8 50%, #e8f2ee 100%); text-align: center; padding: 20mm; }
.cover-tag { background: rgba(90,71,224,0.12); border: 1pt solid rgba(90,71,224,0.35); color: #5a47e0; font-family: 'Space Mono', monospace; font-size: 7pt; letter-spacing: 0.2em; padding: 3pt 10pt; text-transform: uppercase; margin-bottom: 16pt; }
.cover h1 { font-size: 38pt; font-weight: 800; color: #1a1816; letter-spacing: -0.02em; line-height: 1.05; margin-bottom: 6pt; }
.cover h1 span { color: #5a47e0; }
.cover-sub { font-family: 'Space Mono', monospace; font-size: 8pt; color: #7a7670; margin-bottom: 32pt; }
.cover-rule { width: 60mm; height: 1pt; background: #d8d4cc; margin: 0 auto 28pt; }
.cover-info { width: 100%; max-width: 130mm; text-align: left; }
.cover-field { border-bottom: 1.5pt solid #d8d4cc; padding-bottom: 3pt; margin-bottom: 18pt; display: flex; align-items: flex-end; gap: 8pt; }
.cover-field-label { font-family: 'Space Mono', monospace; font-size: 7pt; color: #7a7670; text-transform: uppercase; letter-spacing: 0.12em; min-width: 28mm; }
.cover-field-line { flex: 1; height: 14pt; }
.cover-sections { margin-top: 28pt; border: 1pt solid #d8d4cc; border-radius: 3pt; overflow: hidden; width: 100%; max-width: 130mm; }
.cover-sections-header { background: rgba(90,71,224,0.08); border-bottom: 1pt solid #d8d4cc; padding: 5pt 10pt; font-family: 'Space Mono', monospace; font-size: 6.5pt; color: #5a47e0; text-transform: uppercase; letter-spacing: 0.12em; }
.cover-section-row { display: flex; border-bottom: 1pt solid #d8d4cc; padding: 4pt 10pt; align-items: center; gap: 8pt; }
.cover-section-row:last-child { border-bottom: none; }
.csr-letter { font-family: 'Space Mono', monospace; font-size: 8pt; font-weight: 700; color: #5a47e0; min-width: 10mm; }
.csr-title { flex: 1; font-size: 8pt; color: #2e2c28; }
.csr-pts { font-family: 'Space Mono', monospace; font-size: 7pt; color: #d4600a; }
.csr-score { font-family: 'Space Mono', monospace; font-size: 7pt; color: #7a7670; min-width: 14mm; text-align: right; }
```

### Exam section header

```html
<div class="exam-section">
  <div class="sec-header">
    <span class="sec-letter">A</span>
    <span class="sec-title">Section Title</span>
    <span class="sec-pts">2 pts each × 5 = 10 pts</span>
  </div>
  <div class="sec-instruction">Instruction text.</div>
  <!-- question blocks -->
</div>
```

```css
.exam-section { page-break-before: always; }
.sec-header { background: #2e2c28; color: #f5f4f0; padding: 7pt 12pt; display: flex; align-items: baseline; gap: 10pt; }
.sec-letter { font-family: 'Space Mono', monospace; font-size: 16pt; font-weight: 700; color: #5a47e0; line-height: 1; }
.sec-title { font-size: 10pt; font-weight: 700; flex: 1; }
.sec-pts { font-family: 'Space Mono', monospace; font-size: 7pt; color: #d4600a; background: rgba(212,96,10,0.15); padding: 2pt 6pt; border-radius: 2pt; }
.sec-instruction { background: rgba(90,71,224,0.06); border-left: 3pt solid #5a47e0; padding: 5pt 10pt; font-size: 8.5pt; font-style: italic; margin-bottom: 10pt; }
```

### Question block

```html
<div class="q-block">
  <div class="q-header">
    <span class="q-num">A1</span>
    <span class="q-src">source paper</span>
    <span class="q-pts">2 pts</span>
  </div>
  <div class="q-body">
    <p class="q-text">Question text. <em>[P, Q, R]</em></p>
    <div class="write-space">
      <div class="write-space-label">Your answer</div>
      <div class="write-lines">
        <div class="write-line"></div>
        <div class="write-line"></div>
        <div class="write-line"></div>
      </div>
    </div>
  </div>
</div>
```

Write space sizing:
- Simple answer: 2–3 lines
- Medium working: 5–6 lines
- Short proof (5–8 steps): use `.proof-write` with 8 rows
- Long proof (10+ steps): `.proof-write` with 12 rows
- Truth table: use `.tt-write` with pre-filled variable columns

```css
.q-block { border: 1pt solid #d8d4cc; border-radius: 3pt; margin-bottom: 10pt; overflow: hidden; background: #ffffff; break-inside: avoid; }
.q-header { background: #eeecea; border-bottom: 1pt solid #d8d4cc; padding: 4pt 10pt; display: flex; align-items: center; gap: 8pt; }
.q-num { font-family: 'Space Mono', monospace; font-size: 7pt; font-weight: 700; color: #5a47e0; text-transform: uppercase; }
.q-src { font-family: 'Space Mono', monospace; font-size: 6pt; color: #7a7670; background: rgba(90,71,224,0.07); border: 0.5pt solid rgba(90,71,224,0.2); padding: 1pt 5pt; border-radius: 1.5pt; }
.q-pts { margin-left: auto; font-family: 'Space Mono', monospace; font-size: 7pt; color: #d4600a; font-weight: 700; }
.q-body { padding: 8pt 10pt; }
.q-text { font-size: 9pt; line-height: 1.55; margin-bottom: 6pt; }
.q-text em { font-style: normal; font-family: 'Space Mono', monospace; font-size: 8pt; color: #7a7670; }
.write-space { border: 1pt dashed #d8d4cc; border-radius: 2pt; background: rgba(255,255,255,0.6); margin: 6pt 0 0; }
.write-space-label { font-family: 'Space Mono', monospace; font-size: 6pt; color: #d8d4cc; text-transform: uppercase; letter-spacing: 0.1em; padding: 3pt 6pt; border-bottom: 0.5pt dashed #d8d4cc; }
.write-lines { padding: 4pt 8pt 4pt; }
.write-line { border-bottom: 0.75pt solid #eeecea; height: 12pt; }
.write-line:last-child { border-bottom: none; }
```

### Proof write space

```html
<div class="write-space">
  <div class="write-space-label">Proof / Refutation — number every line and state the rule</div>
  <div class="proof-write">
    <div class="proof-write-row"><span class="pwr-star">*</span><span class="pwr-num">1.</span><span class="pwr-line"></span><span class="pwr-just"></span><span class="pwr-mark"></span></div>
    <!-- repeat for lines 2–N -->
  </div>
</div>
```

```css
.proof-write { font-family: 'Space Mono', monospace; font-size: 8pt; }
.proof-write-row { display: flex; gap: 6pt; align-items: center; border-bottom: 0.5pt solid #eeecea; padding: 2pt 6pt; min-height: 14pt; }
.proof-write-row:last-child { border-bottom: none; }
.pwr-num { color: #d8d4cc; min-width: 12pt; font-size: 7pt; }
.pwr-star { color: #d8d4cc; min-width: 8pt; }
.pwr-line { flex: 1; border-bottom: 0.5pt solid #d8d4cc; min-height: 10pt; }
.pwr-just { color: #d8d4cc; font-size: 6.5pt; min-width: 28mm; border-bottom: 0.5pt solid #d8d4cc; min-height: 10pt; }
.pwr-mark { color: #d8d4cc; font-size: 6.5pt; min-width: 10mm; text-align: right; border-bottom: 0.5pt solid #d8d4cc; }
```

### Truth table write space

```html
<table class="tt-write">
  <thead><tr><th>P</th><th>Q</th><th class="main-th">(P ⊃ Q)</th></tr></thead>
  <tbody>
    <tr><td class="given">0</td><td class="given">0</td><td class="fill main-td"></td></tr>
    <tr><td class="given">0</td><td class="given">1</td><td class="fill main-td"></td></tr>
    <tr><td class="given">1</td><td class="given">0</td><td class="fill main-td"></td></tr>
    <tr><td class="given">1</td><td class="given">1</td><td class="fill main-td"></td></tr>
  </tbody>
</table>
```

```css
.tt-write { border-collapse: collapse; font-family: 'Space Mono', monospace; font-size: 8pt; width: 100%; }
.tt-write th { background: #eeecea; color: #5a47e0; padding: 4pt 6pt; border: 1pt solid #d8d4cc; text-align: center; white-space: nowrap; }
.tt-write th.main-th { background: rgba(90,71,224,0.1); border: 1.5pt solid #5a47e0; }
.tt-write td { border: 1pt solid #d8d4cc; padding: 0; text-align: center; height: 14pt; }
.tt-write td.given { color: #7a7670; font-size: 8pt; vertical-align: middle; }
.tt-write td.fill { background: #ffffff; }
.tt-write td.main-td { background: rgba(90,71,224,0.03); border: 1.5pt solid #5a47e0; }
```

### Scrap page (always last)

```html
<div class="exam-section">
  <div class="sec-header">
    <span class="sec-letter">✎</span>
    <span class="sec-title">Scrap Paper — Working Space</span>
    <span class="sec-pts">not marked</span>
  </div>
  <div style="height: 240mm; border: 1pt dashed #d8d4cc; border-radius: 3pt; background: #ffffff; margin-top: 8pt;"></div>
</div>
```

---

## Interactive Mock Exam HTML (`mock_final.html`)

Structure:
- Sticky score bar fixed top: live score, percentage, letter grade pill, "Reveal All Answers" button
- Each question is a `.q-block[data-pts="N"]` with a contenteditable `.q-work` area, a `.q-reveal` click-to-reveal button, and a `.q-answer` div (hidden until revealed)
- `.self-mark` buttons (✓ / ✗ / partial) update a running `earned` total
- JS: `answered[blockId]` dict tracking self-mark state; grade thresholds (A+ ≥90%, A ≥85%, A- ≥80%, B+ ≥77%, B ≥73%, etc.)

Score bar and question JS pattern:
```html
<div class="score-bar">
  Score: <span id="score-display">0 / TOTAL (0%)</span>
  <span id="grade-pill" class="grade-pill">—</span>
  <button onclick="revealAll()">Reveal All Answers</button>
</div>

<div class="q-block" data-pts="2" id="q1">
  <div class="q-header">...</div>
  <div class="q-work" contenteditable="true">WORK SPACE</div>
  <div class="q-reveal"><div class="icon">?</div><span class="reveal-txt">Reveal model answer</span></div>
  <div class="q-answer">Model answer here.</div>
  <div class="self-mark">
    <button onclick="mark('q1', 2)">✓ Full</button>
    <button onclick="mark('q1', 1)">~ Partial</button>
    <button onclick="mark('q1', 0)">✗ Wrong</button>
  </div>
</div>
```

```javascript
var earned = 0, total = TOTAL_PTS, answered = {};
function mark(id, pts) {
  if (answered[id] !== undefined) earned -= answered[id];
  answered[id] = pts; earned += pts;
  updateScore();
}
function updateScore() {
  var pct = Math.round(earned / total * 100);
  document.getElementById('score-display').textContent = earned + ' / ' + total + ' (' + pct + '%)';
  // grade thresholds: A+≥90, A≥85, A-≥80, B+≥77, B≥73, B-≥70, C+≥67, C≥63, C-≥60, D≥50, F<50
}
function revealAll() {
  document.querySelectorAll('.q-block').forEach(function(b){ b.classList.add('revealed'); });
}
document.querySelectorAll('.q-reveal').forEach(function(r){
  r.addEventListener('click', function(){ r.closest('.q-block').classList.toggle('revealed'); });
});
```

CSS: `.q-answer { display: none; }` → `.q-block.revealed .q-answer { display: block; }`

---

## Rendering

Output files go in the same directory as the source HTML. Change `output_dir` to match the course folder.

```python
import weasyprint, warnings
warnings.filterwarnings('ignore')

output_dir = '/path/to/course/folder'  # set per course

# Study guide
with open(f'{output_dir}/study_notes.html', 'r') as f:
    content = f.read()
print_css = """<style>
@page { size: A4; margin: 12mm 14mm 16mm 14mm; }
body { max-width: 100% !important; font-size: 9pt !important; }
p, li { font-size: 8.5pt !important; }
.nav-strip, .sidebar-toc, .reading-progress { display: none !important; }
.reveal-body, .q-answer, .drill-answer { display: block !important; }
.reveal-hint, .img-overlay, .self-mark, .score-bar { display: none !important; }
.quiz-opt.correct { background: rgba(13,146,100,0.08) !important; }
.quiz-feedback { display: block !important; }
.section-block, .q-block { break-inside: avoid-page; }
</style>"""
content = content.replace('</head>', print_css + '</head>')
with open('/tmp/study_guide_for_pdf.html', 'w') as f:
    f.write(content)
weasyprint.HTML(filename='/tmp/study_guide_for_pdf.html').render().write_pdf(f'{output_dir}/study_guide.pdf')

# Mock exam — show all answers, hide interactive chrome
with open(f'{output_dir}/mock_final.html', 'r') as f:
    content = f.read()
mock_css = """<style>
@page { size: A4; margin: 12mm 14mm 16mm 14mm; }
body { max-width: 100% !important; font-size: 9pt !important; }
.score-bar, .self-mark, .q-reveal { display: none !important; }
.q-answer { display: block !important; }
.q-work { border: 1px solid #ccc; min-height: 40px; margin-bottom: .4rem; }
.q-block { break-inside: avoid-page; }
</style>"""
content = content.replace('</head>', mock_css + '</head>')
with open('/tmp/mock_final_for_pdf.html', 'w') as f:
    f.write(content)
weasyprint.HTML(filename='/tmp/mock_final_for_pdf.html').render().write_pdf(f'{output_dir}/mock_final.pdf')
```
