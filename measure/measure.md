# /measure [page]

Capture a full CSS layout snapshot of the current project. Writes `layout-snapshot.json` to the project root. Requires no code changes — auto-discovers all meaningful elements.

## Arguments
- `page` — optional path/filename to navigate to (default: `/` for dev servers, `index.html` for static sites). Examples: `transactions`, `home`, `complaints/123`, `about.html`

## Steps

### 1. Determine project type and target URL

Check whether `package.json` exists in the current working directory.

**A. Static site (no `package.json`):**
- Locate the entry HTML file: use the `page` argument if it ends in `.html`, otherwise look for `index.html` in the current working directory.
- Set `TARGET_URL = file://<absolute-cwd>/<entry>.html`
- Skip steps 1B and 1C entirely — do NOT probe any ports.

**B. Dev server project (has `package.json`):**
- Determine the target port:
  1. Read `scripts.dev` in `package.json` — if it contains `--port XXXX`, use that port.
  2. Otherwise check `vite.config.ts` or `vite.config.js` for `server.port`.
  3. Otherwise default to `5173`.
- Check what (if anything) is already on that port:
  ```bash
  lsof -i :<port> -n -P | grep LISTEN
  ```
  - **Nothing on the port** → start the dev server fresh: `npm run dev &`, then wait up to 15s for it to respond.
  - **Something is on the port** → get the CWD of the listening process:
    ```bash
    lsof -i :<port> -n -P | grep LISTEN | awk '{print $2}' | xargs -I{} lsof -p {} -Fn | grep '^n' | grep -v '.so' | head -5
    ```
    Compare that CWD against the current working directory:
    - **Same directory (or subdirectory)** → this is already the right project's server. Reuse it — do NOT kill, do NOT restart.
    - **Different directory** → foreign server from another project. Kill it and start fresh:
      ```bash
      lsof -ti :<port> | xargs kill -9 2>/dev/null || true
      npm run dev &
      ```
      Wait up to 15s for the port to respond.
- Set `TARGET_URL = http://localhost:<port>/<page>`

### 2. Open/navigate the page

Use `mcp__chrome-devtools__navigate_page` to `TARGET_URL`.
If no page is open yet, use `mcp__chrome-devtools__new_page` first.

### 3. Wait for content

Inject this poll via `mcp__chrome-devtools__evaluate_script`:

```js
new Promise(r => {
  const check = () => document.readyState === 'complete'
    ? r() : setTimeout(check, 150)
  check()
})
```

Wait an additional 1500ms after that for any skeleton loaders / async renders.

### 4. Choose the measurement script

**If `scripts/measure.js` exists** in the current working directory, read its contents and inject that. It already knows the project's selectors.

**Otherwise** inject the generic auto-discovery script below.

### 5. Inject the script

Use `mcp__chrome-devtools__evaluate_script` with `awaitPromise: true`. Save the returned JSON value.

### 6. Write output

Write the result as formatted JSON to `layout-snapshot.json` in the project root.

### 7. Print summary

Print a concise human-readable summary grouped by element type:
- One line per discovered element: type → w × h, font-size, color
- Highlight any elements with equal heights (likely repeated cards/rows)

### 8. Print efficiency metrics

After the summary, print a compact metrics block showing what the smart routing saved versus the old blind port-probing approach:

```
── /measure efficiency ──────────────────────────────
  Project type : <Static site (file://)  |  Dev server (localhost:PORT)>
  Chrome calls : <N used this run>   (old approach: <N+savings> — saved <savings>)
  Tokens saved : ~<tokens>  (<pct>% reduction)
  Reason       : <one line explaining why savings occurred>
─────────────────────────────────────────────────────
```

Use these reference figures when computing the block:

**Static site (no `package.json` detected):**
- Chrome calls this run: 3 (new_page/navigate + wait + measure)
- Old approach would have used: 7 (port probe hits wrong server → navigate + wait + measure wrong + list_pages + select_page + navigate right + wait + measure right... minus the navigate right which is counted above)
- Chrome calls saved: 4
- Tokens saved: ~57,000 (one full wrong-server snapshot ≈ 56k tokens + ~800 tokens conversation overhead to correct the mistake)
- Token reduction: ~52%
- Reason: "Skipped blind port-probing; navigated directly via file:// — avoided capturing a wrong-project snapshot"

**Dev server — right project already running on port:**
- Chrome calls this run: 3 (navigate + wait + measure)
- Old approach would have used: 3 (same — lucky hit)
- Chrome calls saved: 0 (server reused, no restart needed)
- Tokens saved: ~500 (skipped port-probe Bash round-trips)
- Reason: "Detected current project's server already running on port <PORT> — reused without restart"

**Dev server — foreign server on port (killed + restarted):**
- Chrome calls this run: 3 (navigate + wait + measure)
- Old approach would have used: 7 (navigated to wrong server, measured it, realised mistake, redid everything)
- Chrome calls saved: 4
- Tokens saved: ~57,000
- Reason: "Killed foreign server on port <PORT> (different project CWD), started project-specific dev server"

**Dev server — port was free:**
- Chrome calls this run: 3 (navigate + wait + measure)
- Old approach would have used: 4 (port probe Bash + navigate + wait + measure)
- Chrome calls saved: 1
- Tokens saved: ~200
- Reason: "Port <PORT> was free; started dev server directly"

---

## Generic auto-discovery script (no data-measure needed)

```js
(() => {
  const STYLE_PROPS = [
    'width','height','minWidth','maxWidth','minHeight','maxHeight',
    'paddingTop','paddingRight','paddingBottom','paddingLeft',
    'marginTop','marginRight','marginBottom','marginLeft',
    'borderTopWidth','borderRightWidth','borderBottomWidth','borderLeftWidth',
    'borderTopStyle','borderRightStyle','borderBottomStyle','borderLeftStyle',
    'borderTopColor','borderRightColor','borderBottomColor','borderLeftColor',
    'borderTopLeftRadius','borderTopRightRadius','borderBottomRightRadius','borderBottomLeftRadius',
    'boxSizing','boxShadow',
    'display','flexDirection','flexWrap','justifyContent','alignItems','alignSelf',
    'gap','rowGap','columnGap','gridTemplateColumns',
    'position','top','right','bottom','left','zIndex',
    'overflow','overflowX','overflowY',
    'fontFamily','fontSize','fontWeight','fontStyle','lineHeight','letterSpacing',
    'textAlign','textDecoration','textTransform','textOverflow','whiteSpace',
    'color','backgroundColor','backgroundImage','opacity',
    'cursor','pointerEvents','visibility','aspectRatio','transform','transition',
  ]

  function capture(el) {
    if (!el) return null
    const r = el.getBoundingClientRect()
    const cs = getComputedStyle(el)
    const out = { _rect: { w: Math.round(r.width), h: Math.round(r.height), x: Math.round(r.x), y: Math.round(r.y) } }
    for (const p of STYLE_PROPS) out[p] = cs[p] || null
    return out
  }

  const seen = new WeakSet()
  const collected = []

  function add(el, discoveredBy) {
    if (!el || seen.has(el)) return
    const tag = el.tagName.toLowerCase()
    if (tag === 'html' || tag === 'body' || tag === 'script' || tag === 'style') return
    const r = el.getBoundingClientRect()
    if (r.width < 8 || r.height < 8) return
    seen.add(el)
    collected.push({
      discoveredBy,
      tag,
      id: el.id || null,
      classes: (el.getAttribute('class') || '').slice(0, 100) || null,
      text: (el.textContent || '').trim().replace(/\s+/g, ' ').slice(0, 60) || null,
      area: Math.round(r.width * r.height),
      ...capture(el),
    })
  }

  // 1. data-measure attributes (explicit project tags — highest priority)
  document.querySelectorAll('[data-measure]').forEach(el =>
    add(el, 'data-measure:' + el.dataset.measure))

  // 2. Semantic HTML
  ;['nav','header','main','aside','footer','section','article','form'].forEach(tag =>
    document.querySelectorAll(tag).forEach(el => add(el, 'semantic:' + tag)))

  // 3. ARIA roles
  ;['navigation','banner','main','complementary','contentinfo',
    'dialog','alertdialog','toolbar','tablist','listbox','grid','search'].forEach(role =>
    document.querySelectorAll(`[role="${role}"]`).forEach(el => add(el, 'role:' + role)))

  // 4. Class name patterns — common design system / UI component names
  ;[
    'card','tile','panel','widget',
    'navbar','sidebar','topbar','toolbar','nav-',
    'modal','drawer','dialog','overlay','popup','dropdown','popover',
    'table','data-table',
    'btn','button','cta',
    'badge','tag','chip','pill','label',
    'alert','toast','notification','banner',
    'avatar','icon-btn',
    'tabs','tab-','breadcrumb','pagination','stepper',
    'input','search','filter',
    'skeleton','spinner','loader','empty-state',
    'header','footer','layout','container','content','wrapper','inner',
  ].forEach(pattern =>
    document.querySelectorAll(`[class*="${pattern}"]`).forEach(el => add(el, 'class:' + pattern)))

  // 5. All interactive elements
  document.querySelectorAll('button, input, select, textarea, a[href], [tabindex]').forEach(el =>
    add(el, 'interactive:' + el.tagName.toLowerCase()))

  // 6. Top 30 remaining elements by visual area (catches anything missed above)
  const candidates = Array.from(document.querySelectorAll('*'))
    .filter(el => !seen.has(el))
    .map(el => ({ el, r: el.getBoundingClientRect() }))
    .filter(({ r }) => r.width > 60 && r.height > 60)
    .sort((a, b) => (b.r.width * b.r.height) - (a.r.width * a.r.height))
    .slice(0, 30)
  candidates.forEach(({ el }) => add(el, 'area-rank'))

  // Sort: data-measure first, then by vertical position
  collected.sort((a, b) => {
    const aTagged = a.discoveredBy.startsWith('data-measure') ? 0 : 1
    const bTagged = b.discoveredBy.startsWith('data-measure') ? 0 : 1
    if (aTagged !== bTagged) return aTagged - bTagged
    return (a._rect?.y ?? 0) - (b._rect?.y ?? 0)
  })

  return {
    _meta: {
      timestamp: new Date().toISOString(),
      url: location.href,
      viewport: { w: window.innerWidth, h: window.innerHeight },
      totalElements: collected.length,
    },
    elements: collected,
  }
})()
```
