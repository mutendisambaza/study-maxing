# Interactive Components Reference

All interactive components use the design system palette from DESIGN_SYSTEM.md.
All interactivity is powered by the JS block at the bottom of this file.
The PDF print CSS in DESIGN_SYSTEM.md ensures every interactive element degrades gracefully to static content in print.

---

## 1. Click-to-Reveal Definition Card

Use for every definition. Replace plain blockquotes with reveal cards.

```html
<div class="reveal">
  <div class="reveal-prompt">What is <em>Term Name</em>?</div>
  <div class="reveal-hint">Try to recall the answer, then click to reveal.</div>
  <div class="reveal-body">Full definition text here.</div>
</div>
```

```css
.reveal {
  border: 1.5px dashed var(--accent);
  background: linear-gradient(135deg, rgba(90,71,224,0.04) 0%, rgba(90,71,224,0.08) 100%);
  border-radius: 4px; padding: 1rem 1.2rem; margin: 1rem 0;
  cursor: pointer; transition: all 0.2s; position: relative;
}
.reveal:hover { box-shadow: 0 2px 10px rgba(90,71,224,0.12); border-color: var(--accent2); }
.reveal-prompt {
  font-weight: 700; color: var(--text-bright); font-size: 0.9rem;
  display: flex; align-items: center; gap: 0.6rem;
}
.reveal-prompt::before {
  content: "?"; display: inline-flex; align-items: center; justify-content: center;
  width: 20px; height: 20px; border-radius: 50%;
  background: var(--accent); color: white; font-size: 0.75rem; font-weight: 700; flex-shrink: 0;
}
.reveal-hint { font-size: 0.72rem; color: var(--text-dim); font-style: italic; margin-top: 0.4rem; }
.reveal-body { display: none; margin-top: 0.9rem; padding-top: 0.9rem; border-top: 1px solid var(--border); font-size: 0.88rem; color: var(--text); }
.reveal.open { background: rgba(13,146,100,0.05); border-style: solid; border-color: var(--accent3); cursor: default; }
.reveal.open .reveal-prompt::before { content: "✓"; background: var(--accent3); }
.reveal.open .reveal-hint { display: none; }
.reveal.open .reveal-body { display: block; }
```

---

## 2. Hide-the-Diagram Overlay

Applied automatically by the image embedding script. Every embedded slide gets this.

```html
<div class="img-block hideable">
  <img src="data:image/png;base64,..." alt="Diagram caption">
  <div class="caption">Figure: Diagram caption</div>
  <div class="img-overlay" role="button" tabindex="0">
    <div class="img-overlay-title">Diagram caption</div>
    <div class="img-overlay-hint">Try to recall or sketch this diagram. Click to reveal.</div>
  </div>
</div>
```

```css
.img-block { text-align: center; margin: 1.5rem 0; break-inside: avoid; position: relative; }
.img-block img { max-width: 100%; max-height: 400px; border: 1px solid var(--border); border-radius: 4px; object-fit: contain; }
.img-block .caption { font-family: 'Space Mono', monospace; font-size: 0.65rem; color: var(--text-dim); margin-top: 0.4rem; }
.img-block.hideable img { filter: blur(14px); transition: filter 0.4s; }
.img-block.hideable .img-overlay {
  position: absolute; inset: 0; display: flex; align-items: center;
  justify-content: center; flex-direction: column; gap: 8px;
  background: rgba(245,244,240,0.88); border-radius: 4px;
  cursor: pointer; text-align: center; padding: 1.5rem;
}
.img-overlay-title { font-weight: 700; color: var(--text-bright); font-size: 0.9rem; }
.img-overlay-hint { font-size: 0.72rem; color: var(--text-dim); font-style: italic; }
.img-block.revealed img { filter: none; }
.img-block.revealed .img-overlay { display: none; }
```

---

## 3. Cloze Deletion

> **Not a standard output.** Do not include in default builds. Only use if the course has verbatim-recall requirements (specific sequences, formulas, acronyms) and the user requests it.

For critical mnemonics, rules, and sequences the student must memorize. Use 1–3 per section.

```html
The TDD cycle is
<span class="cloze" data-answer="Red">Red</span> →
<span class="cloze" data-answer="Green">Green</span> →
<span class="cloze" data-answer="Refactor">Refactor</span>.
```

```css
.cloze {
  display: inline-block; background: var(--text-bright); color: var(--text-bright);
  padding: 1px 10px; border-radius: 3px; cursor: pointer; user-select: none;
  font-weight: 600; min-width: 50px; text-align: center; transition: all 0.2s;
}
.cloze:hover { background: var(--accent); color: rgba(90,71,224,0.2); }
.cloze.open { background: rgba(212,96,10,0.10); color: var(--accent2); border: 1px solid rgba(212,96,10,0.3); padding: 0 9px; }
```

---

## 4. MCQ Quiz

Insert at the end of every major content section (every `section-block`). 3–5 questions.

```html
<div class="quiz" id="quiz-s1">
  <div class="quiz-header">Section 01 — Topic Name</div>
  <div class="quiz-q">
    <div class="q-text">Question text here?</div>
    <ul class="quiz-opts">
      <li class="quiz-opt" data-letter="A">Option A</li>
      <li class="quiz-opt" data-letter="B" data-correct="1">Correct option</li>
      <li class="quiz-opt" data-letter="C">Option C</li>
      <li class="quiz-opt" data-letter="D">Option D</li>
    </ul>
    <div class="quiz-feedback"><strong>Why:</strong> Explanation of why B is correct.</div>
  </div>
  <!-- repeat .quiz-q for remaining questions -->
</div>
```

Exactly one `data-correct="1"` per question. Feedback must explain the *why*, not just confirm.

```css
.quiz {
  background: rgba(90,71,224,0.04); border: 1.5px solid rgba(90,71,224,0.25);
  border-radius: 4px; padding: 1.4rem 1.6rem; margin: 2rem 0; break-inside: avoid;
}
.quiz-header {
  font-family: 'Space Mono', monospace; font-size: 0.65rem; font-weight: 700;
  color: var(--accent); text-transform: uppercase; letter-spacing: 0.1em;
  margin-bottom: 1rem; display: flex; align-items: center; gap: 0.6rem;
}
.quiz-header::before {
  content: "QUICK CHECK";
  background: var(--accent); color: white;
  padding: 0.2rem 0.6rem; border-radius: 2px; font-size: 0.58rem; letter-spacing: 0.08em;
}
.quiz-q { margin: 1rem 0; padding-top: 1rem; border-top: 1px solid rgba(90,71,224,0.12); }
.quiz-q:first-of-type { border-top: none; padding-top: 0; }
.q-text { font-weight: 700; color: var(--text-bright); font-size: 0.88rem; margin-bottom: 0.6rem; }
.quiz-opts { list-style: none; padding: 0; margin: 0; }
.quiz-opt {
  background: var(--surface); border: 1px solid var(--border);
  border-radius: 3px; padding: 0.55rem 0.9rem; margin: 0.4rem 0;
  cursor: pointer; font-size: 0.85rem; transition: all 0.15s;
  display: flex; align-items: flex-start; gap: 0.6rem;
}
.quiz-opt::before {
  content: attr(data-letter);
  display: inline-flex; align-items: center; justify-content: center;
  width: 18px; height: 18px; border-radius: 50%;
  background: var(--surface2); border: 1px solid var(--border);
  font-family: 'Space Mono', monospace; font-weight: 700; color: var(--text-dim); font-size: 0.6rem; flex-shrink: 0;
}
.quiz-opt:hover:not(.locked) { border-color: var(--accent); background: rgba(90,71,224,0.04); }
.quiz-opt.correct { border-color: var(--accent3); background: rgba(13,146,100,0.06); }
.quiz-opt.correct::before { background: var(--accent3); color: white; border-color: var(--accent3); }
.quiz-opt.incorrect { border-color: var(--accent4); background: rgba(200,48,74,0.05); }
.quiz-opt.incorrect::before { background: var(--accent4); color: white; border-color: var(--accent4); }
.quiz-opt.locked { cursor: default; }
.quiz-feedback {
  display: none; margin-top: 0.7rem; padding: 0.7rem 0.9rem;
  background: var(--surface); border-radius: 3px; border-left: 3px solid var(--accent3);
  font-size: 0.82rem; color: var(--text);
}
.quiz-feedback.show { display: block; }
.quiz-feedback.wrong { border-left-color: var(--accent4); }
```

---

## 5. Flip Card Grid

> **Not a standard output.** Do not include in default builds — flip cards inflate PDFs and do not mirror exam conditions. Only use when the course has a large catalogue to memorise (design patterns, anatomy terms, case law, etc.) AND the user explicitly requests them.

For catalogues with 5+ items (design patterns, architecture styles, UML diagram types, etc.). Place immediately after the catalogue introduction.

```html
<div class="flipcard-grid">
  <div class="flipcard">
    <div class="flipcard-inner">
      <div class="flipcard-front">
        <div class="fc-cat">Category</div>
        <div class="fc-name">Pattern Name</div>
        <div class="fc-problem">One-sentence problem this solves.</div>
        <div class="fc-hint">click to flip →</div>
      </div>
      <div class="flipcard-back">
        <div class="fc-section"><div class="fc-label">Purpose</div><p>What it does.</p></div>
        <div class="fc-section"><div class="fc-label">When to use</div><p>Context.</p></div>
        <div class="fc-section"><div class="fc-label">Key indicator</div><p>How to recognise it.</p></div>
      </div>
    </div>
  </div>
  <!-- repeat per item -->
</div>
```

```css
.flipcard-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(240px, 1fr)); gap: 1rem; margin: 1.5rem 0; }
.flipcard { perspective: 1000px; min-height: 220px; cursor: pointer; }
.flipcard-inner { position: relative; width: 100%; min-height: 220px; transition: transform 0.55s; transform-style: preserve-3d; }
.flipcard.flipped .flipcard-inner { transform: rotateY(180deg); }
.flipcard-front, .flipcard-back {
  position: absolute; inset: 0; backface-visibility: hidden;
  border-radius: 4px; padding: 1.1rem; display: flex; flex-direction: column;
  box-shadow: 0 2px 8px rgba(0,0,0,0.07); overflow-y: auto;
}
.flipcard-front {
  background: linear-gradient(135deg, #5a47e0 0%, #3d2faa 100%);
  color: white; align-items: center; justify-content: center; text-align: center;
}
.fc-cat { font-family: 'Space Mono', monospace; font-size: 0.58rem; text-transform: uppercase; letter-spacing: 0.15em; color: rgba(255,255,255,0.65); margin-bottom: 0.5rem; }
.fc-name { font-size: 1rem; font-weight: 800; color: #f5e9c0; margin-bottom: 0.6rem; }
.fc-problem { font-size: 0.78rem; font-style: italic; line-height: 1.4; color: rgba(255,255,255,0.88); }
.fc-hint { position: absolute; bottom: 8px; right: 12px; font-family: 'Space Mono', monospace; font-size: 0.6rem; opacity: 0.5; }
.flipcard-back {
  background: var(--surface); border: 2px solid rgba(90,71,224,0.3);
  transform: rotateY(180deg); overflow-y: auto;
}
.fc-section { margin-bottom: 0.6rem; }
.fc-label { font-family: 'Space Mono', monospace; font-size: 0.6rem; text-transform: uppercase; letter-spacing: 0.1em; color: var(--accent2); font-weight: 700; margin-bottom: 0.15rem; }
.fc-section p { font-size: 0.8rem; color: var(--text); line-height: 1.4; margin: 0; }
```

---

## 6. Matching Game

> **Not a standard output.** Do not include in default builds. Only use when the course has 5+ natural pairs to learn (term ↔ definition, law ↔ case, concept ↔ example) AND the user explicitly requests it.

For 5+ pair relationships (pattern → category, term → definition, etc.). Place at the end of the relevant section.

```html
<div class="match-game" id="match-patterns">
  <div class="match-label">MATCH THE PAIRS</div>
  <h4>Match each pattern to its category</h4>
  <div class="match-instruct">Click one tile from each column. Correct pairs lock green; wrong pairs shake.</div>
  <div class="match-grid">
    <div class="match-col">
      <div class="match-tile" data-pair="0" data-side="L">Observer</div>
      <div class="match-tile" data-pair="1" data-side="L">Factory Method</div>
      <div class="match-tile" data-pair="2" data-side="L">Adapter</div>
    </div>
    <div class="match-col">
      <!-- right side SHUFFLED — pair indices do not match row order -->
      <div class="match-tile" data-pair="1" data-side="R">Creational</div>
      <div class="match-tile" data-pair="2" data-side="R">Structural</div>
      <div class="match-tile" data-pair="0" data-side="R">Behavioural</div>
    </div>
  </div>
  <div class="match-status"></div>
</div>
```

```css
.match-game { background: rgba(90,71,224,0.04); border: 1.5px solid rgba(90,71,224,0.2); border-radius: 4px; padding: 1.4rem 1.6rem; margin: 2rem 0; break-inside: avoid; }
.match-label { display: inline-block; background: var(--accent); color: white; padding: 0.2rem 0.6rem; border-radius: 2px; font-family: 'Space Mono', monospace; font-size: 0.58rem; font-weight: 700; letter-spacing: 0.08em; margin-bottom: 0.5rem; }
.match-game h4 { font-size: 0.9rem; color: var(--text-bright); margin: 0.3rem 0 0.4rem; }
.match-instruct { font-family: 'Space Mono', monospace; font-size: 0.65rem; color: var(--text-dim); margin-bottom: 1rem; }
.match-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 0.8rem; }
.match-col { display: flex; flex-direction: column; gap: 0.5rem; }
.match-tile {
  background: var(--surface); border: 1px solid var(--border); border-radius: 3px;
  padding: 0.6rem 0.9rem; cursor: pointer; font-size: 0.82rem;
  transition: all 0.15s; min-height: 36px; display: flex; align-items: center;
}
.match-tile:hover:not(.matched) { border-color: var(--accent); background: rgba(90,71,224,0.05); }
.match-tile.selected { border-color: var(--accent); background: rgba(90,71,224,0.08); box-shadow: 0 0 0 2px rgba(90,71,224,0.2); }
.match-tile.matched { background: rgba(13,146,100,0.07); border-color: var(--accent3); cursor: default; }
.match-tile.matched::after { content: " ✓"; color: var(--accent3); font-weight: 700; margin-left: auto; }
.match-tile.wrong { background: rgba(200,48,74,0.06); border-color: var(--accent4); animation: shake 0.4s; }
@keyframes shake { 0%,100% { transform: translateX(0); } 25% { transform: translateX(-4px); } 75% { transform: translateX(4px); } }
.match-status { margin-top: 0.8rem; font-family: 'Space Mono', monospace; font-size: 0.65rem; color: var(--text-dim); text-align: center; }
.match-status.complete { color: var(--accent3); font-weight: 700; }
```

---

## 7. Predict-the-Output

For code blocks where the output is deterministic. Wrap the `<pre>` in a predict block.

```html
<div class="predict">
  <div class="predict-prompt">If the client runs <code>greet("Alice")</code>, what does this print?</div>
  <pre><code>def greet(name):
    print(f"Hello, {name}!")</code></pre>
  <button class="predict-btn" type="button">Reveal Output</button>
  <div class="predict-output">Hello, Alice!</div>
</div>
```

Do NOT use this for type definitions, interfaces, or non-runnable code.

```css
.predict { background: rgba(212,96,10,0.04); border: 1.5px dashed var(--accent2); border-radius: 4px; padding: 1.1rem 1.4rem; margin: 1.5rem 0; break-inside: avoid; }
.predict::before { content: "PREDICT THE OUTPUT"; display: inline-block; background: var(--accent2); color: white; padding: 0.2rem 0.6rem; border-radius: 2px; font-family: 'Space Mono', monospace; font-size: 0.58rem; font-weight: 700; letter-spacing: 0.08em; margin-bottom: 0.6rem; }
.predict-prompt { font-size: 0.85rem; color: var(--text); margin-bottom: 0.7rem; }
.predict-output { display: none; background: #2e2c28; color: #eeecea; padding: 0.8rem 1rem; border-radius: 3px; font-family: 'Space Mono', monospace; font-size: 0.78rem; line-height: 1.5; margin-top: 0.8rem; white-space: pre-wrap; }
.predict-output.show { display: block; }
.predict-btn { background: var(--accent2); color: white; border: none; padding: 0.45rem 1rem; border-radius: 3px; font-family: 'Syne', sans-serif; font-weight: 700; font-size: 0.82rem; cursor: pointer; margin-top: 0.5rem; }
.predict-btn:hover { background: #b8500a; }
.predict-btn.hidden { display: none; }
```

---

## JavaScript Block

Insert immediately before `</body>` in every generated HTML file. Powers all interactive features above.

```html
<script>
(function(){
  // Reading progress bar
  var pb = document.querySelector('.reading-progress');
  if (pb) {
    window.addEventListener('scroll', function(){
      var h = document.documentElement;
      pb.style.width = Math.max(0, Math.min(100, h.scrollTop / (h.scrollHeight - h.clientHeight) * 100)) + '%';
    }, { passive: true });
  }

  // Sidebar TOC scroll-spy
  var sidebarLinks = document.querySelectorAll('.sidebar-toc a[href^="#"]');
  if (sidebarLinks.length && 'IntersectionObserver' in window) {
    var linkMap = {};
    sidebarLinks.forEach(function(a){ linkMap[a.getAttribute('href').slice(1)] = a; });
    var io = new IntersectionObserver(function(entries){
      entries.forEach(function(e){
        if (e.isIntersecting && linkMap[e.target.id]) {
          sidebarLinks.forEach(function(l){ l.classList.remove('active'); });
          linkMap[e.target.id].classList.add('active');
        }
      });
    }, { rootMargin: '-30% 0px -60% 0px' });
    Object.keys(linkMap).forEach(function(id){ var el = document.getElementById(id); if (el) io.observe(el); });
  }

  // Reveal cards
  document.querySelectorAll('.reveal').forEach(function(card){
    card.addEventListener('click', function(){ if (!card.classList.contains('open')) card.classList.add('open'); });
  });

  // Hide-the-diagram
  document.querySelectorAll('.img-block.hideable .img-overlay').forEach(function(ov){
    ov.addEventListener('click', function(){ ov.parentElement.classList.add('revealed'); });
    ov.addEventListener('keydown', function(e){ if (e.key === 'Enter' || e.key === ' ') ov.parentElement.classList.add('revealed'); });
  });

  // Cloze deletions
  document.querySelectorAll('.cloze').forEach(function(c){
    c.addEventListener('click', function(){ c.classList.toggle('open'); });
  });

  // MCQ quizzes
  document.querySelectorAll('.quiz-q').forEach(function(q){
    var opts = q.querySelectorAll('.quiz-opt');
    var fb = q.querySelector('.quiz-feedback');
    opts.forEach(function(opt){
      opt.addEventListener('click', function(){
        if (q.dataset.answered) return;
        q.dataset.answered = '1';
        var correct = opt.dataset.correct === '1';
        opt.classList.add(correct ? 'correct' : 'incorrect');
        opts.forEach(function(o){
          o.classList.add('locked');
          if (o.dataset.correct === '1') o.classList.add('correct');
        });
        if (fb){ fb.classList.add('show'); if (!correct) fb.classList.add('wrong'); }
      });
    });
  });

  // Flip cards
  document.querySelectorAll('.flipcard').forEach(function(c){
    c.addEventListener('click', function(){ c.classList.toggle('flipped'); });
  });

  // Matching games
  document.querySelectorAll('.match-game').forEach(function(game){
    var tiles = game.querySelectorAll('.match-tile');
    var status = game.querySelector('.match-status');
    var total = tiles.length / 2, matched = 0, selected = null;
    if (status) status.textContent = '0 / ' + total + ' matched';
    tiles.forEach(function(tile){
      tile.addEventListener('click', function(){
        if (tile.classList.contains('matched')) return;
        if (!selected) { selected = tile; tile.classList.add('selected'); return; }
        if (selected === tile) { tile.classList.remove('selected'); selected = null; return; }
        if (selected.dataset.pair === tile.dataset.pair && selected.dataset.side !== tile.dataset.side) {
          [selected, tile].forEach(function(t){ t.classList.remove('selected'); t.classList.add('matched'); });
          matched++;
          if (status) { status.textContent = matched + ' / ' + total + ' matched'; if (matched === total) { status.textContent = 'All ' + total + ' pairs matched! ✓'; status.classList.add('complete'); } }
          selected = null;
        } else {
          var prev = selected; selected = null;
          [prev, tile].forEach(function(t){ t.classList.add('wrong'); });
          setTimeout(function(){ [prev, tile].forEach(function(t){ t.classList.remove('wrong', 'selected'); }); }, 500);
        }
      });
    });
  });

  // Predict the output
  document.querySelectorAll('.predict').forEach(function(p){
    var btn = p.querySelector('.predict-btn'), out = p.querySelector('.predict-output');
    if (btn && out) btn.addEventListener('click', function(){ out.classList.add('show'); btn.classList.add('hidden'); });
  });
})();
</script>
```
