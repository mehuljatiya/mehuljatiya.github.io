# Video KYC Case Study — Design Critique
Date: 2026-05-25
Score: 22/40 (Functional — below portfolio ambition)

---

## Design Health Score

| # | Heuristic | Score | Finding |
|---|-----------|-------|---------|
| 1 | Visibility of System Status | 3/4 | Numbered section labels (01-10) help orientation, but there is no jump nav — reader has no sense of position while scrolling |
| 2 | Match Between System and Real World | 3/4 | Language is generally accessible; "Ecosystem" in the title is slightly inflated for what is a multi-surface product |
| 3 | User Control and Freedom | 2/4 | No section anchor links; hiring managers have no fast path to Results without scrolling the full page |
| 4 | Consistency and Standards | 3/4 | Token system is internally solid, but the identical container treatment makes every section visually equivalent |
| 5 | Error Prevention | 2/4 | YouTube thumbnail `::after` label ("Watch on YouTube") sits below the play button, creating intent mismatch; no fallback state if Figma iframes fail to load |
| 6 | Recognition Rather Than Recall | 3/4 | Section numbers and challenge-num prefixes help; prototype embeds lack priming copy to set reader expectations |
| 7 | Flexibility and Efficiency | 1/4 | Zero jump links, no keyboard navigation beyond focus-visible, no fast path for power readers scanning for results |
| 8 | Aesthetic and Minimalist Design | 2/4 | Mechanical rhythm disguised as minimalism; Playfair Display absent (present on index.html); ten sections with identical templates is maximalist not minimalist |
| 9 | Error Recovery | 1/4 | No loading states on Figma embeds; tech-grid cards with 4-8 word content are too thin to recover reader comprehension of what was actually built |
| 10 | Help and Documentation | 2/4 | "What this unlocks" bullet list and the impact block directly below it repeat the same information in different formats |
| **Total** | | **22/40** | **Functional** |

---

## AI Slop Verdict: YES — with one genuine exception

The page hits the template signals:
- Four-insight 2x2 grid (Insight 01-04) — platonic AI research synthesis
- Four-pillar row (Speed / Clarity / Accuracy / Trust) with one-line subtitles — a design template, not a point of view
- Leadership 2x2 grid (Influence > Authority / Owning the Narrative / Mentorship / Strategic Design) — genericized to meaninglessness
- Repeated `.cards-grid`, `.insights-grid`, `.leadership-grid`, `.tech-grid` — four CSS class names, one visual treatment (#161616 bg, 1px border, 8px radius, 1.25-1.5rem padding)
- The `section-heading` with a left blue bar applied to every section without variation
- No section breathes differently from any other

The one genuine exception: the Hinglish merchant quote in section 10. Specific, unscripted, with a translation. This is the only element that could not have been generated from a template.

---

## Automated Detector Findings

3 findings, exit code 2:

**1. `side-tab` — side-tab accent border (warning)**
- `.section-heading { border-left: 3px solid var(--blue) }` — heading usage is a partial false positive in isolation
- `.callout { border-left: 3px solid var(--blue) }` — same pattern doing two different jobs (headings + callouts) is the real issue; it flattens hierarchy between structural element and pull-quote

**2. `pure-black-white` — pure black background (warning)**
- `.yt-embed { background: #000 }` — CONFIRMED FALSE POSITIVE. Black background for a video player is conventional and correct.

**Additional finding from browser inspection (not caught by detector):**
The Agent Workspace and Auditor Portal prototype sections embed dark UI screenshots inside `background: var(--bg)` (#161616) containers. The product UI inside those frames is also predominantly dark. At scroll speed the sections appear as dark rectangles — the most important execution proof becomes nearly invisible. This is a significant credibility gap.

---

## Genuine Strengths

**1. The merchant conversation block (section 10)**
One real person speaking in Hinglish with an inline translation. Specific, unscripted, earns the V2 narrative. Does what no card grid can. This is the most memorable moment in the document.

**2. The engineering negotiation story (section 08)**
The actual stakeholder quote ("Let's meet in the middle...") plus the concrete outcome (component-first architecture) makes a credible case for influence-without-authority. Most portfolios only assert this quality; this one shows the friction.

**3. Structural coherence across four user surfaces**
Splitting execution into 04.1 / 04.2 / 04.3 rather than one "Design" dump is a considered IA decision that respects the reader's time and clarifies scope of work.

---

## Priority Issues

### P0: Playfair Display is absent — the brand voice stops at the door

**What:** The index.html establishes a two-font identity (Kumbh Sans + Playfair Display). The case study loads only Kumbh Sans. Every heading renders flat.

**Why it matters:** A portfolio where design IS the product cannot have a different personality at the case study level. The moment someone clicks through from the index, the register drops. It signals that the case study was assembled separately from the rest of the portfolio.

**Fix:** Import Playfair Display. Apply to `.hero h1` and `.section-heading h2`. At minimum the hero — "VIDEO KYC / ECOSYSTEM." in Playfair at 3.75rem — immediately elevates the page into the same register as the index.

**Suggested command:** `/impeccable typeset`

---

### P1: Identical container grid addiction destroys pacing and credibility

**What:** `.cards-grid`, `.insights-grid`, `.leadership-grid`, `.tech-grid` — four class names, one visual output. By section 05 the reader's eye has learned every grid is interchangeable. The tech-grid cards in section 08 with 4-word content ("Video Stability / Real-time video performance") are the low point — too thin to carry information, they damage credibility rather than build it.

**Why it matters:** The page's visual rhythm is mechanical. Nothing signals that one section's content is more important than another. The strongest moments (Results, the engineering quote) receive the same container as the weakest (the pillars row).

**Fix:** Where a grid adds nothing over prose, replace it with prose. Specifically: the pillars row (Speed / Clarity / Accuracy / Trust with one-line captions) and the two tech-grids in section 08 should become short paragraphs or a single well-structured callout. Reserve card treatment for two or three genuinely comparative layouts. The contrast will make those sections feel deliberate.

**Suggested command:** `/impeccable layout`

---

### P1: Results section has the best numbers and the least visual impact

**What:** 80% conversion (from 50%) and 3-minute call time (from 8 minutes) — a 60% conversion lift and a 62% call-time reduction. These are presented in a clean stat block with the same visual weight as every other container on the page. "Sixty Days to Success." is close to right as a heading but the section doesn't deliver on it.

**Why it matters:** The numbers are the payoff of the entire narrative. A hiring manager who jumps to Results (which they will) should feel the scale of the outcome. Right now it reads like a product spec, not a proof of impact.

**Fix:** Redesign section 06 as a distinct peak moment:
- The headline stat (80%) should be significantly larger than all other numbers on the page
- Before/after deltas (from 50%, from 8m) should be visually prominent, not buried in a 0.6875rem `lbl` class
- White space before and after the section should signal a chapter close — this is the moment everything was building toward
- Consider removing the border treatment entirely from this section so it stands apart visually

**Suggested command:** `/impeccable bolder`

---

### P2: Dark UI mockups on dark background — execution proof disappears

**What:** The Agent Workspace (section 04.2) and Auditor Portal (section 04.3) Figma embed containers use `background: var(--bg)` (#161616). The product UI in those frames is also predominantly dark. At scroll speed these sections render as dark rectangles. Browser screenshot confirmed nearly zero contrast.

**Why it matters:** These are the sections that should prove execution capability. If the reader cannot see the work, the section actively hurts rather than helps — it signals there is nothing to show.

**Fix:** Two options:
1. Add a light or mid-tone background to the embed containers (`background: #1e2028` or similar blue-tinted dark that creates separation) and a visible inner border
2. Add a top bar/label strip above each embed showing what the viewer is looking at ("Agent Workspace — Figma Prototype") so the embed context is clear even before it loads

**Suggested command:** `/impeccable polish`

---

### P2: Copy is vague where specificity builds credibility

**What:** Multiple insight and challenge statements are conclusions without evidence:
- "Existing solutions had inconsistent UX and unreliable performance." — Which solutions? What inconsistency was measured?
- "Agent delays created user frustration." — How long were delays? What research surfaced this?
- "Customers were abandoning the journey during personal detail collection due to lack of guidance." — What drop-off rate? At which step?
- "Review workflows were unintuitive, leading to compliance slips and manual rework." — Measured how?

**Why it matters:** A design director reading in depth registers assertions immediately. The insights read as fabricated rather than researched. This undermines the seniority the rest of the page is trying to establish — especially given the strong concrete evidence in sections 08 and 10.

**Fix:** For each insight, either (a) name the research method and add one concrete observation or number, or (b) reframe explicitly as a hypothesis the design was built to address. The distinction between insight and hypothesis matters at a senior level and actually demonstrates more rigor, not less.

**Suggested command:** `/impeccable clarify`

---

## Persona Red Flags

### Hiring Manager (30-60 second scan)

No hook in the hero. The first paragraph is: "A regulated digital verification system that lets financial institutions verify customer identity through a live video session." That is a definition, not a reason to keep reading.

The leadership claim ("Even though I was not a Design Manager at that time, I led the initiative at a manager level") appears in paragraph two without evidence yet — it reads as defensive self-promotion rather than demonstrated confidence.

The best proof points (the numbers, the stakeholder quote, the merchant conversation) are all below the fold with no visual shortcut to reach them.

### Design Director (reading in depth)

No process artifact beyond prototypes. The Discovery section presents conclusions with no research method cited. Every challenge in section 07 resolved cleanly with no pivot, no moment of being wrong, no learning that changed the approach mid-project. That reads as sanitized rather than reflective.

Sections 07 and 08 cover leadership from two angles with significant overlap — the Leadership Growth cards in 07 and the full "Leadership in Action" section in 08 cover the same territory. This signals the author assembled the narrative rather than edited it.

### PM or Founder (assessing design leadership credibility)

The V2 section presents one merchant conversation as "validated our hypothesis." A single qualitative data point is a signal worth pursuing, not validation. A founder will flag this conflation of signal with proof — it suggests the author conflates insight with validation, which undermines the strategic credibility the rest of the page is building toward.

---

## Minor Observations (fix in a polish pass)

1. **The self-congratulatory callout (section 09):** "This was not just a production task. This was design leadership ensuring the story we told the world matched the product we had created." — this is the author describing themselves via a styled block meant for quotes. It reads as self-congratulatory when not attributed to anyone. Replace with plain paragraph text.

2. **Figma filename visible in URLs:** `Customer-Journey-final_dec-2025` appears in the iframe src and open-link href. "final_dec-2025" is an amateur signal in a context where everything else is trying to signal seniority. Not fixable without re-publishing the Figma file, but worth noting.

3. **Redundant site-link-box copy:** The `.site-link-box` for the Cashfree landing page has a "Live Website" tag and then copy that reads "Visit the launch page designed and built on Framer." Both say the same thing. One should go.

4. **V2 prototype is an afterthought:** The V2 prototype embed sits after the impact block and bullet list. The narrative has already closed before the prototype appears. Repositioning it above the bullet list would make V2 feel forward-looking rather than appended.

5. **Sections 07 and 08 overlap:** The Leadership Growth 2x2 in section 07 and all of section 08 cover leadership without new information in the second pass. Collapsing section 07 into a single paragraph would remove the redundancy and give section 08 more impact.

6. **ALL-CAPS hero heading:** "VIDEO KYC" in heavy white caps reads as shouting. At 3.75rem, mixed-case in Playfair would carry the same weight with more sophistication.

---

## Questions to Resolve

1. If you removed all seven card grids and rewrote their content as prose, would anything be lost — or would the page become more confident?

2. The two strongest moments in the document are both unscripted: a real stakeholder quote and a real merchant speaking Hinglish. Why is the designed structure working against those moments rather than amplifying them?

3. The results are a 60% conversion lift and a 62% call-time reduction. Why do those numbers receive the same visual weight as the company tag chip?

4. The Discovery section presents four insights with no research method cited. Would naming the method (even briefly: "from 12 agent interviews" or "from session recordings") make the claims feel more earned, or is there no research to cite?

---

## Recommended Fix Sequence

1. **`/impeccable typeset`** — Import Playfair Display, apply to hero h1 and section h2. Single highest-impact change.
2. **`/impeccable layout`** — Replace thin card grids with prose in sections 03, 08 (tech-grids). Vary visual treatment across sections so hierarchy is readable.
3. **`/impeccable bolder`** — Redesign Results section (06) as the visual peak moment. Larger stats, prominent deltas, distinct spacing.
4. **`/impeccable clarify`** — Rewrite the four Discovery insights with method + one concrete observation each. Rewrite challenge resolutions to be specific.
5. **`/impeccable polish`** — Fix embed container contrast, remove redundant copy, move V2 prototype above bullet list, collapse sections 07+08 overlap.
6. **Re-run `/impeccable critique`** to see score improvement.
