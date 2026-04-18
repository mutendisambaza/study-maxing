# Design System Reference

Follow every rule here exactly. The goal is pixel-perfect consistency across all outputs.

---

## Colour Palette

```css
:root {
  --bg:          #f5f4f0;   /* warm off-white page background */
  --surface:     #ffffff;   /* card / block backgrounds */
  --surface2:    #eeecea;   /* table headers, code blocks, secondary surfaces */
  --border:      #d8d4cc;   /* all borders */
  --accent:      #5a47e0;   /* purple — primary accent, links, section numbers */
  --accent2:     #d4600a;   /* burnt orange — marks, points, tips */
  --accent3:     #0d9264;   /* forest green — valid, correct, success */
  --accent4:     #c8304a;   /* crimson — invalid, warnings, errors */
  --text:        #2e2c28;   /* body text */
  --text-dim:    #7a7670;   /* secondary text, labels, captions */
  --text-bright: #1a1816;   /* headings, emphasized text */
}
```

**Never add new colours.** Everything uses the variables above.

---

## Typography

```css
body { font-family: 'Syne', sans-serif; }
code, .proof-table, .tt, .ftable, .vsteps, .sidebar-toc { font-family: 'Space Mono', monospace; }
```

Google Fonts import (always include):
```html
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:ital,wght@0,400;0,700;1,400&family=Syne:wght@400;600;700;800&display=swap" rel="stylesheet">
```

Font size scale:
- Hero h1: `clamp(2rem, 5vw, 3.2rem)`, weight 800
- Section h2: `1.15rem`, weight 700
- Body: `0.88–0.9rem`
- Monospace labels: `0.65–0.72rem`
- Mark badges: `0.58–0.6rem`

---

## Layout

```css
body { background: var(--bg); color: var(--text); max-width: 980px; margin: 0 auto; padding: 0 1.5rem 4rem; }
.container { max-width: 980px; margin: 0 auto; padding: 2rem 1.5rem 4rem; }
```

---

## Core Component Library

### Reading Progress Bar + Sidebar TOC

Insert immediately after `<body>`:
```html
<div class="reading-progress"></div>
<nav class="sidebar-toc" aria-label="Section navigation">
  <h4>Sections</h4>
  <ol>
    <li><a href="#s1">1. Topic</a></li>
    <!-- one per top-level section -->
  </ol>
</nav>
```

```css
.reading-progress {
  position: fixed; top: 0; left: 0; height: 3px; width: 0%;
  background: linear-gradient(90deg, var(--accent), var(--accent2));
  z-index: 9999; transition: width 0.1s ease-out;
}
.sidebar-toc {
  position: fixed; top: 60px; left: 16px;
  width: 220px; max-height: calc(100vh - 80px);
  overflow-y: auto; padding: 14px 12px;
  background: var(--surface); border: 1px solid var(--border);
  border-left: 3px solid var(--accent);
  border-radius: 4px; font-size: 0.68rem; z-index: 100;
  box-shadow: 0 4px 14px rgba(0,0,0,0.06);
}
.sidebar-toc h4 {
  font-size: 0.6rem; text-transform: uppercase; letter-spacing: 0.15em;
  color: var(--text-dim); margin-bottom: 10px; font-weight: 700;
}
.sidebar-toc ol { list-style: none; padding-left: 0; margin: 0; }
.sidebar-toc li { margin: 3px 0; }
.sidebar-toc a {
  color: var(--text); text-decoration: none; display: block;
  padding: 3px 7px; border-radius: 2px; border-left: 2px solid transparent;
  transition: all 0.15s;
}
.sidebar-toc a:hover { background: rgba(90,71,224,0.06); color: var(--accent); }
.sidebar-toc a.active {
  background: rgba(90,71,224,0.08); color: var(--accent); font-weight: 700;
  border-left-color: var(--accent);
}
@media (max-width: 1280px) { .sidebar-toc { display: none; } }
```

---

### Hero

```html
<div class="hero">
  <div class="hero-tag">COURSE CODE · Topic</div>
  <h1>Guide <span>Title</span></h1>
  <p class="hero-sub">subtitle in Space Mono</p>
</div>
```

```css
.hero {
  background: linear-gradient(135deg, #edeaf4 0%, #f0edf8 50%, #e8f2ee 100%);
  border-bottom: 1px solid var(--border);
  padding: 3rem 2rem 2.5rem; text-align: center;
}
.hero h1 { font-size: clamp(2rem,5vw,3.2rem); font-weight: 800; color: var(--text-bright); }
.hero h1 span { color: var(--accent); }
.hero-tag {
  background: rgba(90,71,224,0.10); border: 1px solid rgba(90,71,224,0.3);
  color: var(--accent); font-family: 'Space Mono', monospace;
  font-size: 0.7rem; letter-spacing: 0.15em;
  padding: 0.25rem 0.8rem; border-radius: 2px;
  text-transform: uppercase; margin-bottom: 1rem; display: inline-block;
}
.hero-sub { font-family: 'Space Mono', monospace; font-size: 0.75rem; color: var(--text-dim); margin-top: 0.5rem; }
```

---

### Section Block

```html
<div class="section-block" id="s1">
  <div class="section-header">
    <span class="section-num">01</span>
    <h2>Section Title</h2>
    <span class="pts">N pts each</span>
  </div>
  <div class="section-body"><!-- content --></div>
</div>
```

```css
.section-block { margin-bottom: 3.5rem; border: 1px solid var(--border); border-radius: 4px; overflow: hidden; }
.section-header { display: flex; align-items: center; gap: 1rem; padding: 1rem 1.4rem; background: var(--surface2); border-bottom: 1px solid var(--border); }
.section-num { font-family: 'Space Mono', monospace; font-size: 0.65rem; color: var(--accent); background: rgba(90,71,224,0.12); border: 1px solid rgba(90,71,224,0.3); padding: 0.2rem 0.5rem; border-radius: 2px; }
.section-header h2 { font-size: 1.15rem; font-weight: 700; color: var(--text-bright); }
.section-header .pts { margin-left: auto; font-family: 'Space Mono', monospace; font-size: 0.65rem; color: var(--accent2); }
.section-body { padding: 1.4rem; }
```

---

### Analogy, Warning, Tip Boxes

```html
<div class="analogy"><div class="analogy-tag">🔑 Key Insight</div>Content.</div>
<div class="warn"><div class="warn-tag">⚠ Common Mistake</div>Content.</div>
<div class="tip"><div class="tip-tag">💡 Exam Tip</div>Content.</div>
```

```css
.analogy { background: rgba(13,146,100,0.06); border-left: 3px solid var(--accent3); border-radius: 0 4px 4px 0; padding: 0.9rem 1.1rem; margin: 1rem 0; font-size: 0.88rem; }
.analogy-tag { font-family: 'Space Mono', monospace; font-size: 0.6rem; color: var(--accent3); text-transform: uppercase; letter-spacing: 0.1em; margin-bottom: 0.35rem; }
.warn { background: rgba(200,48,74,0.06); border-left: 3px solid var(--accent4); border-radius: 0 4px 4px 0; padding: 0.9rem 1.1rem; margin: 1rem 0; font-size: 0.88rem; }
.warn-tag { font-family: 'Space Mono', monospace; font-size: 0.6rem; color: var(--accent4); text-transform: uppercase; letter-spacing: 0.1em; margin-bottom: 0.35rem; }
.tip { background: rgba(212,96,10,0.06); border-left: 3px solid var(--accent2); border-radius: 0 4px 4px 0; padding: 0.9rem 1.1rem; margin: 1rem 0; font-size: 0.88rem; }
.tip-tag { font-family: 'Space Mono', monospace; font-size: 0.6rem; color: var(--accent2); text-transform: uppercase; letter-spacing: 0.1em; margin-bottom: 0.35rem; }
```

---

### Formula / Reference Table (ftable)

```html
<table class="ftable">
  <thead><tr><th>Concept</th><th>Symbol</th><th>Notes</th></tr></thead>
  <tbody>
    <tr><td class="eng">English label</td><td class="sym">Formula</td><td class="note-cell">Explanation</td></tr>
  </tbody>
</table>
```

```css
.ftable { width: 100%; border-collapse: collapse; margin: 1rem 0; font-family: 'Space Mono', monospace; font-size: 0.78rem; }
.ftable th { background: var(--surface2); color: var(--accent); text-align: left; padding: 0.5rem 0.8rem; border-bottom: 1px solid var(--border); font-weight: 400; font-size: 0.65rem; text-transform: uppercase; letter-spacing: 0.08em; }
.ftable td { padding: 0.5rem 0.8rem; border-bottom: 1px solid rgba(216,212,204,0.6); color: var(--text); vertical-align: top; }
.ftable tr:last-child td { border-bottom: none; }
.sym { color: var(--accent2); font-weight: 700; }
.eng { color: var(--text-bright); }
.note-cell { color: var(--text-dim); font-size: 0.7rem; }
```

---

### Step-by-Step / Derivation Table

Use for any multi-step reasoning: logical proofs, algorithm traces, derivations, worked calculations, proof-by-contradiction, induction steps. Column headers adapt to the domain (Formula/Statement/Expression, Justification/Rule/Reason). **Never use monospace pre blocks.**

The example below uses logic notation — replace column names and content for other subjects (e.g. "Statement / Reason" for geometry, "Line / Expression / Rule" for algebra, "Step / Operation / Result" for algorithms).

```html
<table class="proof-table">
  <thead><tr><th>★</th><th>#</th><th>Statement</th><th>Justification</th><th class="pt-mark">Mark</th></tr></thead>
  <tbody>
    <tr><td class="pt-star">*</td><td class="pt-num">1</td><td class="pt-form">(P ⊃ Q)</td><td class="pt-justif">Premise</td><td class="pt-mark">—</td></tr>
    <tr><td class="pt-star"></td><td class="pt-num">2</td><td class="pt-form asm-form">asm: ~Q</td><td class="pt-justif">Assume negation</td><td class="pt-mark">½</td></tr>
    <tr><td class="pt-star"></td><td class="pt-num">3</td><td class="pt-form der-form">~P</td><td class="pt-justif">{from 1 & 2, MT}</td><td class="pt-mark">1</td></tr>
    <tr class="verdict-row"><td colspan="4">VALID ✓ — contradiction found</td><td class="pt-mark">5 pts</td></tr>
  </tbody>
</table>
```

```css
.proof-table { width: 100%; border-collapse: collapse; font-family: 'Space Mono', monospace; font-size: 0.75rem; margin: 0.8rem 0; background: #eeecea; border: 1px solid var(--border); border-radius: 4px; overflow: hidden; }
.proof-table thead th { background: var(--surface2); color: var(--text-dim); font-size: 0.6rem; text-transform: uppercase; letter-spacing: 0.1em; padding: 0.4rem 0.7rem; text-align: left; border-bottom: 1px solid var(--border); font-weight: 400; }
.proof-table td { padding: 0.32rem 0.7rem; border-bottom: 1px solid rgba(216,212,204,0.5); vertical-align: middle; }
.proof-table tr:last-child td { border-bottom: none; }
.pt-num { color: var(--text-dim); width: 1.8rem; }
.pt-star { color: var(--accent); width: 1rem; font-weight: 700; }
.pt-form { color: var(--text-bright); font-weight: 700; }
.pt-form.asm-form { color: var(--accent4); }
.pt-form.der-form { color: var(--accent3); }
.pt-justif { color: var(--text-dim); font-size: 0.65rem; }
.pt-mark { font-size: 0.6rem; color: var(--accent2); font-weight: 700; text-align: right; width: 3.5rem; white-space: nowrap; }
.proof-table tr.verdict-row td { background: rgba(13,146,100,0.08); color: var(--accent3); font-weight: 700; border-top: 2px solid var(--accent3); padding: 0.5rem 0.7rem; }
.proof-table tr.invalid-row td { background: rgba(200,48,74,0.07); color: var(--accent4); font-weight: 700; border-top: 2px solid var(--accent4); padding: 0.5rem 0.7rem; }
.proof-table tr.section-divider td { background: var(--surface2); color: var(--text-dim); font-size: 0.6rem; text-transform: uppercase; letter-spacing: 0.1em; padding: 0.25rem 0.7rem; }
```

---

### Boolean / Decision Table (tt)

Use for truth tables (logic), input/output tables (CS), decision matrices (any domain). **Never use monospace pre blocks.**

```html
<div class="tt-wrap">
  <table class="tt">
    <thead><tr><th>P</th><th>Q</th><th class="main-col">(P ⊃ Q)</th></tr></thead>
    <tbody>
      <tr><td class="val-0">0</td><td class="val-0">0</td><td class="main-val val-1">1</td></tr>
    </tbody>
  </table>
</div>
<p class="tt-note">← explanation</p>
```

```css
.tt-wrap { overflow-x: auto; margin: 0.8rem 0; }
.tt { border-collapse: collapse; font-family: 'Space Mono', monospace; font-size: 0.73rem; background: #eeecea; border: 1px solid var(--border); border-radius: 4px; overflow: hidden; min-width: max-content; }
.tt th { background: var(--surface2); color: var(--accent); padding: 0.4rem 0.8rem; border-bottom: 2px solid var(--border); text-align: center; font-weight: 700; white-space: nowrap; }
.tt th.main-col { background: rgba(90,71,224,0.08); border-bottom: 2px solid var(--accent); }
.tt td { padding: 0.28rem 0.8rem; border-bottom: 1px solid rgba(216,212,204,0.5); text-align: center; color: var(--text); }
.tt td.val-1 { color: var(--accent3); font-weight: 700; }
.tt td.val-0 { color: var(--accent4); }
.tt td.main-val { background: rgba(90,71,224,0.04); font-weight: 700; }
.tt td.flag { background: rgba(200,48,74,0.07); }
.tt tr:last-child td { border-bottom: none; }
.tt-note { font-family: 'Space Mono', monospace; font-size: 0.65rem; color: var(--text-dim); margin-top: 0.3rem; }
```

---

### Step Sequence (vsteps)

Use for any ordered procedure: validity tests, algorithm steps, exam technique sequences, test procedures, installation steps, proof strategy. Labels adapt to the domain — `.force`, `.extract`, `.check`, `.contra`, `.result`, `.invalid` are the available colour variants; use the label text that fits your subject.

```html
<div class="vsteps">
  <div class="vstep"><span class="vstep-lbl force">Step 1</span><span class="vstep-content">Description of what to do first.</span><span class="vmark">1 pt</span></div>
  <div class="vstep"><span class="vstep-lbl extract">Step 2</span><span class="vstep-content">What to check or extract.</span><span class="vmark">½ pt</span></div>
  <div class="vstep"><span class="vstep-lbl contra">Step 3</span><span class="vstep-content">What condition fails or is satisfied.</span><span class="vmark">1 pt</span></div>
  <div class="vstep"><span class="vstep-lbl result">Result</span><span class="vstep-content"><strong>Verdict or conclusion.</strong></span><span class="vmark">1 pt</span></div>
</div>
```

```css
.vsteps { background: #eeecea; border: 1px solid var(--border); border-radius: 4px; overflow: hidden; margin: 0.8rem 0; font-family: 'Space Mono', monospace; font-size: 0.75rem; }
.vstep { display: flex; gap: 0.8rem; align-items: flex-start; padding: 0.38rem 0.8rem; border-bottom: 1px solid rgba(216,212,204,0.5); }
.vstep:last-child { border-bottom: none; }
.vstep-lbl { font-size: 0.58rem; padding: 0.12rem 0.4rem; border-radius: 1.5px; white-space: nowrap; min-width: 5.5rem; text-align: center; align-self: flex-start; margin-top: 0.1rem; text-transform: uppercase; letter-spacing: 0.06em; background: var(--surface2); color: var(--text-dim); }
.vstep-lbl.force   { background: rgba(90,71,224,0.1);  color: var(--accent);  }
.vstep-lbl.extract { background: rgba(13,146,100,0.1); color: var(--accent3); }
.vstep-lbl.check   { background: rgba(212,96,10,0.1);  color: var(--accent2); }
.vstep-lbl.contra  { background: rgba(200,48,74,0.1);  color: var(--accent4); font-weight: 700; }
.vstep-lbl.result  { background: rgba(13,146,100,0.15);color: var(--accent3); font-weight: 700; }
.vstep-lbl.invalid { background: rgba(200,48,74,0.1);  color: var(--accent4); font-weight: 700; }
.vstep-content { flex: 1; color: var(--text); line-height: 1.5; font-size: 0.75rem; }
.vstep-content strong { color: var(--text-bright); }
.vmark { color: var(--accent2); font-size: 0.65rem; font-weight: 700; white-space: nowrap; padding-top: 0.1rem; }
```

---

### Paper Source Tag & Mark Badge

```html
<div class="paper-src">📄 test2_f19 · Section B Q3</div>
<span class="ms">2 pts</span>
```

```css
.paper-src { display: inline-flex; align-items: center; gap: 0.4rem; background: rgba(90,71,224,0.07); border: 1px solid rgba(90,71,224,0.2); border-radius: 2px; padding: 0.2rem 0.6rem; font-family: 'Space Mono', monospace; font-size: 0.6rem; color: var(--accent); margin-bottom: 0.7rem; text-transform: uppercase; letter-spacing: 0.08em; }
.ms { display: inline-block; background: rgba(212,96,10,0.12); color: var(--accent2); border: 1px solid rgba(212,96,10,0.3); font-family: 'Space Mono', monospace; font-size: 0.58rem; padding: 0.08rem 0.35rem; border-radius: 2px; margin-left: 0.3rem; font-weight: 700; letter-spacing: 0.05em; vertical-align: middle; }
```

---

### Cards Grid

```html
<div class="cards">
  <div class="card"><div class="card-label green">Label</div><p>Content.</p></div>
  <div class="card"><div class="card-label orange">Label</div><p>Content.</p></div>
</div>
```

```css
.cards { display: grid; grid-template-columns: repeat(auto-fit, minmax(260px, 1fr)); gap: 1rem; margin-bottom: 1.2rem; }
.card { background: var(--surface2); border: 1px solid var(--border); border-radius: 4px; padding: 1rem 1.1rem; }
.card-label { font-family: 'Space Mono', monospace; font-size: 0.6rem; text-transform: uppercase; letter-spacing: 0.12em; margin-bottom: 0.5rem; }
.card-label.blue   { color: var(--accent);  }
.card-label.orange { color: var(--accent2); }
.card-label.green  { color: var(--accent3); }
.card-label.red    { color: var(--accent4); }
.card p { font-size: 0.88rem; color: var(--text); line-height: 1.55; }
```

---

### Divider

```html
<div class="divider"><span>label</span></div>
```

```css
.divider { display: flex; align-items: center; gap: 1rem; margin: 2.5rem 0; }
.divider::before, .divider::after { content: ''; flex: 1; height: 1px; background: var(--border); }
.divider span { font-family: 'Space Mono', monospace; font-size: 0.65rem; color: var(--text-dim); text-transform: uppercase; letter-spacing: 0.15em; white-space: nowrap; }
```

---

### Nav Strip (interactive HTML only)

```html
<nav class="nav-strip">
  <a href="#s1">Topic 1</a>
</nav>
```

```css
.nav-strip { display: flex; gap: 0.5rem; flex-wrap: wrap; justify-content: center; padding: 1.2rem 1rem; background: #ffffff; border-bottom: 1px solid var(--border); position: sticky; top: 0; z-index: 100; }
.nav-strip a { font-family: 'Space Mono', monospace; font-size: 0.68rem; text-decoration: none; padding: 0.35rem 0.9rem; border-radius: 2px; border: 1px solid var(--border); color: var(--text-dim); transition: all 0.2s; text-transform: uppercase; letter-spacing: 0.08em; }
.nav-strip a:hover { border-color: var(--accent); color: var(--accent); background: rgba(90,71,224,0.08); }
```

Remove nav-strip from PDFs with `display: none !important` in print CSS.

---

## Print CSS (inject before WeasyPrint render)

```css
@page { size: A4; margin: 12mm 14mm 16mm 14mm; }
@page :first { margin-top: 8mm; }
body { max-width: 100% !important; }
.container { max-width: 100% !important; padding: 0 !important; }
.nav-strip, .sidebar-toc, .reading-progress { display: none !important; }
/* Hide all interactive scaffolding — show content */
.reveal-body { display: block !important; }
.reveal-hint, .reveal-prompt::before { display: none !important; }
.img-overlay { display: none !important; }
.img-block.hideable img { filter: none !important; }
.cloze { background: white !important; color: var(--accent4) !important; border-bottom: 1.5px solid var(--accent4); padding: 0 4px; }
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
.card, .analogy, .warn, .tip, .ftable, .proof-table, .vsteps, .tt-wrap, .mock-q { break-inside: avoid; }
@page { @bottom-center { content: counter(page); font-family: 'Space Mono', monospace; font-size: 7pt; color: #7a7670; } }
body { font-size: 9pt !important; }
p, li { font-size: 8.5pt !important; }
```
