# Case Study Design Guidelines

This document is the canonical design spec for all case studies in this portfolio. Read it in full before creating or editing any case study page. The video-kyc case study (`video-kyc/index.html`) is the reference implementation — when in doubt, match it exactly.

---

## Typography

### Font stack
```html
<link href="https://fonts.googleapis.com/css2?family=Kumbh+Sans:wght@300;400;500;600;700&family=Playfair+Display:ital,wght@0,700;1,400&display=swap" rel="stylesheet">
```

Two fonts only:
- **Kumbh Sans** — body, UI labels, meta, captions, everything except headings
- **Playfair Display** — hero h1 and all section h2 headings only

Do NOT use Caveat, Inter, or any other font.

### CSS variables
```css
--font: 'Kumbh Sans', sans-serif;
--serif: 'Playfair Display', Georgia, serif;
```

### Hero h1
```css
.hero h1 {
  font-family: var(--serif);
  font-size: clamp(2.625rem, 6.5vw, 4.25rem);
  font-weight: 700;
  line-height: 1.03;
  letter-spacing: -0.015em;
  color: var(--text);
  margin-bottom: 2rem;
}
.hero h1 .dim {
  font-style: italic;
  font-weight: 400;
  color: rgba(242,242,242,0.42);
  display: block;
}
```

Hero h1 HTML pattern:
```html
<h1>
  Primary title here.
  <span class="dim">Italic subtitle here.</span>
</h1>
```

### Section h2
```css
.section-heading h2 {
  font-family: var(--serif);
  font-size: clamp(1.625rem, 3.5vw, 2.5rem);
  font-weight: 700;
  line-height: 1.1;
  letter-spacing: -0.01em;
  color: var(--text);
}
.section-heading h2 span {
  font-style: italic;
  font-weight: 400;
}
```

Section heading HTML pattern:
```html
<div class="section-heading">
  <h2 id="section-id">Bold part.<span>Italic continuation.</span></h2>
</div>
```

---

## Color Tokens

```css
:root {
  --blue:   #1F67F1;
  --text:   #f2f2f2;
  --muted:  rgba(242,242,242,0.62);
  --bg:     #161616;
  --page:   #080808;
  --border: rgba(255,255,255,0.10);
  --font: 'Kumbh Sans', sans-serif;
  --serif: 'Playfair Display', Georgia, serif;
  --gutter: max(1.5rem, calc((100% - 1080px) / 2));
}
```

Never use `--white` as a token name. The dark base is always `--page`.

---

## Layout

### Body width
```css
body {
  max-width: clamp(760px, 65vw, 1040px);
  margin: 0 auto;
}
```

Never use a fixed `max-width: 1080px`.

### Horizontal padding
```css
.nav,
.hero,
.content {
  padding-left: 2.5rem;
  padding-right: 2.5rem;
  width: 100%;
}
```

### Section spacing
```css
.section {
  padding-top: 4.5rem;
  width: 100%;
}
.section:last-child { padding-bottom: 4.5rem; }
```

### Cover image
Place the cover image between sticky-nav and hero, or within the hero area. Wrap it in `.cover-img`:
```css
.cover-img {
  width: 100%;
  display: block;
  padding: 0 2.5rem;
  border-bottom: 1px solid var(--border);
  overflow: hidden;
}
.cover-img img {
  width: 100%;
  max-width: 720px;
  height: auto;
  display: block;
  margin: -24px auto;
  border-radius: 8px;
}
```

---

## Components

### Section label pill (before each section heading)
```css
.section-label {
  display: inline-flex;
  align-items: center;
  background: rgba(31, 103, 241, 0.15);
  color: var(--blue);
  font-size: 0.6875rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.18em;
  padding: 0.3rem 0.75rem;
  border-radius: 100px;
  margin-bottom: 1.5rem;
}
```

```html
<div class="section-label">Section Name</div>
```

### Callout (pull-quote or framing statement)
Full border, no side stripes:
```css
.callout {
  border: 1px solid rgba(31, 103, 241, 0.3);
  border-radius: 8px;
  padding: 1.25rem 1.5rem;
  background: rgba(31, 103, 241, 0.07);
  margin: 2rem 0;
  font-size: clamp(1rem, 1.8vw, 1.125rem);
  font-weight: 400;
  color: var(--text);
  line-height: 1.7;
  width: 100%;
}
.callout.dark {
  border-color: var(--border);
  background: rgba(255,255,255,0.04);
  color: var(--muted);
}
```

### Figma prototype embed
Use `.proto-embed` with a `.proto-header` label strip so dark UI doesn't disappear on dark background:
```html
<div class="proto-embed desktop">
  <div class="proto-header">
    <span class="proto-header-title">Screen name — what user is looking at</span>
    <span class="proto-header-tag">Figma Prototype</span>
  </div>
  <iframe src="..." height="500" allowfullscreen></iframe>
</div>
<a class="proto-open-link" href="..." target="_blank" rel="noopener">Open in Figma ↗</a>
```

```css
.proto-embed {
  margin: 2rem 0 0.5rem;
  width: 100%;
  border: 1px solid rgba(255,255,255,0.13);
  border-radius: 10px;
  overflow: hidden;
  background: oklch(13% 0.008 240);
}
.proto-embed.desktop iframe { aspect-ratio: 16/9; }
.proto-embed.mobile { max-width: 400px; margin: 2rem auto 0.5rem; }
.proto-embed.mobile iframe { aspect-ratio: 9/18; }
```

### Site preview (clickable screenshot link)
```html
<a class="site-preview" href="URL" target="_blank" rel="noopener noreferrer">
  <div class="site-preview-header">
    <span class="site-preview-url">domain.com/path</span>
    <span class="site-preview-tag">↗ Open live site</span>
  </div>
  <img src="screenshot.jpg" alt="Description">
</a>
```

### Bullet list
```html
<ul class="bullet-list">
  <li>Item text</li>
</ul>
```

Blue dot markers, no side stripes.

### Insights grid (2x2)
Use `.insights-grid` with `.insight-item` elements that have a numbered pill, title, and body. Always add a source attribution line above the grid:
```html
<p class="insight-source">From [research method] with [participants].</p>
<div class="insights-grid">
  <div class="insight-item">
    <span class="insight-num">Insight 01</span>
    <p class="insight-title">Finding title</p>
    <p class="insight-body">Specific observation with evidence, not just a conclusion.</p>
  </div>
</div>
```

### Sticky nav (back button)
```html
<div class="sticky-nav">
  <a href="../index.html">
    <svg width="14" height="14" viewBox="0 0 14 14" fill="none">
      <path d="M8.5 2.5L4 7l4.5 4.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </svg>
    Back to portfolio
  </a>
</div>
```

### Page transition overlay
Every case study must include this at the end of `<body>` for the push-left/right transition with the index:

```html
<div id="pt-overlay"></div>
<script>
(function () {
  var overlay = document.getElementById('pt-overlay');
  var DUR = 340;
  var EASE_IN  = 'transform ' + DUR + 'ms cubic-bezier(0.4,0,1,1)';
  var EASE_OUT = 'transform ' + DUR + 'ms cubic-bezier(0,0,0.2,1)';
  function move(x, trans) {
    overlay.style.transition = trans || 'none';
    overlay.style.transform  = 'translateX(' + x + ')';
  }
  if (sessionStorage.getItem('pt') === 'forward') {
    sessionStorage.removeItem('pt');
    move('0%');
    requestAnimationFrame(function () {
      requestAnimationFrame(function () { move('-100%', EASE_OUT); });
    });
  } else { move('100%'); }
  var backBtn = document.querySelector('.sticky-nav a');
  if (backBtn) {
    backBtn.addEventListener('click', function (e) {
      var href = backBtn.getAttribute('href');
      if (!href) return;
      e.preventDefault();
      move('-100%');
      requestAnimationFrame(function () {
        requestAnimationFrame(function () {
          overlay.style.pointerEvents = 'all';
          move('0%', EASE_IN);
          sessionStorage.setItem('pt', 'back');
          setTimeout(function () { window.location.href = href; }, DUR);
        });
      });
    });
  }
})();
</script>
```

CSS:
```css
#pt-overlay {
  position: fixed; inset: 0; background: #080808;
  z-index: 10000; pointer-events: none;
  will-change: transform; transform: translateX(100%);
}
```

The `index.html` must also have the matching forward-direction variant — see `index.html` for the full implementation.

---

## Absolute Bans

These patterns are forbidden. If you are about to write any of these, stop and rewrite:

1. **Side-stripe borders.** Never use `border-left` or `border-right` greater than 1px as a colored accent on any element (headings, callouts, cards, list items). Replace with full borders, background tints, or nothing.

2. **Identical card grids.** Never repeat the same card template (icon + heading + text) across more than one section. If content doesn't need comparison, use prose or a table instead.

3. **Gradient text.** No `background-clip: text` with a gradient. Use a single solid color.

4. **Em dashes.** Never use `—` anywhere: copy, HTML content, code comments, or markdown. Rewrite the sentence with a comma, colon, or period instead.

5. **`--white` as a CSS variable name.** Use `--page` for the near-black page background.

6. **Fixed `max-width: 1080px` on body.** Use `clamp(760px, 65vw, 1040px)`.

7. **Self-congratulatory callouts.** Never put the author's own reflection inside a styled callout block. Plain paragraph text only.

8. **Assertion-only insights.** Every discovery insight must either name a research method ("from 12 agent interviews") or reframe as a hypothesis ("our assumption was..."). Bare conclusions with no source damage credibility.

---

## Content Guidelines

### Hero paragraphs
- Para 1: open with the business problem, not a product definition.
- Para 2: lead with the outcome number, then the leadership claim. Never open with a hedge ("Even though I was not...").

### Results section
This is the visual peak of every case study. Treat it differently:
- The headline metric should be the largest number on the page
- Before/after deltas must be visually prominent
- Add more whitespace before and after this section than anywhere else
- Consider removing the border treatment so it stands apart

### Leadership claims
Show, don't assert. A stakeholder quote or a concrete negotiation outcome is worth more than four cards about leadership qualities.

### V2 / iteration sections
Never use the word "validated" for a single data point. Use "pointed to," "surfaced," or "confirmed the direction." Validation requires multiple data points across different sources.

---

## Linking from Portfolio Index

Case study cards in `index.html` must link using trailing-slash directory paths:
```html
<a href="video-kyc/" ...>
<a href="1-click-onboarding/" ...>
```

Never use `index.html` suffix in the href. `npx serve` redirects those and breaks relative asset paths.

---

## Impeccable Commands Reference

These are the ordered commands to run when creating or improving a case study:

| Order | Command | What it fixes |
|---|---|---|
| 1 | `/impeccable typeset` | Import Playfair Display, apply to hero h1 and section h2 |
| 2 | `/impeccable layout` | Fix spacing rhythm, replace thin card grids with prose |
| 3 | `/impeccable bolder` | Redesign results section as visual peak moment |
| 4 | `/impeccable clarify` | Rewrite vague insights with method + specific observation |
| 5 | `/impeccable polish` | Fix embed contrast, redundant copy, prototype placement |
| 6 | `/impeccable critique` | Score the page and identify remaining gaps |

Run `/impeccable critique` last to measure progress. Target score: 30/40 minimum.

---

## File Structure

```
mehulportfolio/
  index.html                   — portfolio index (has pt-overlay for forward transition)
  CASE-STUDY-GUIDELINES.md     — this file
  video-kyc/
    index.html                 — reference implementation
    cashfree-vkyc-preview.jpg  — site preview screenshot
    assets/                    — hero and prototype images
  1-click-onboarding/
    index.html
    Mockup 17.png              — cover image
```

New case studies go in their own subdirectory: `mehulportfolio/[slug]/index.html`. Add a card to `index.html` with `href="[slug]/"`.
