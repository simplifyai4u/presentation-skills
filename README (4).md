# Deck Skills — Usage Guide

Two skills that turn an idea into a polished, self-contained HTML presentation:

1. **`deck-outline`** — plans the deck slide-by-slide (the storyboard).
2. **`deck-build`** — hand-crafts the slides into one portable `.html` file.

They chain: outline first, build second. You can also run `deck-build` straight from a
brief, but a quick outline almost always produces a tighter deck.

The finished deck is a single HTML file that behaves like presentation software — light/dark
themes, keyboard controls, animated vector visuals, TCS branding, and PDF export **and**
print that match the screen exactly.

---

## 1. Install

Each skill ships as a `.skill` file (`deck-outline.skill`, `deck-build.skill`).

- **In Claude:** open the `.skill` file card and click **Save skill**. It installs into your
  profile and triggers automatically when your request matches (see trigger phrases below).
- **Manual / Claude Code:** unzip each `.skill` (it's a zip) into your skills directory so
  the path is `deck-build/SKILL.md`, etc.

Once installed you don't call them by name — just describe what you want and the matching
skill activates. You can still name them explicitly ("use the deck-build skill") if you like.

---

## 2. Quick start

```
You: Outline a 10-slide deck on migrating our data platform to Azure, for engineering leaders.
→ deck-outline produces the storyboard.

You: Looks good — build it.
→ deck-build hand-crafts the slides and returns platform-deck.html
```

Open the returned file in a browser, press `F` for fullscreen, `D` to switch themes, and
`E` or `P` to export.

---

## 3. `deck-outline` — the planning pass

### Triggers on
"outline a deck on…", "plan a presentation about…", "structure my slides", "storyboard a
talk", or any time you hand over content to be shaped into a presentation.

### What it asks (only what it can't infer)
Topic & angle · audience · the single takeaway · length (minutes or slide count) · tone ·
must-includes · branding. If you give it a document or a clear brief, it extracts answers
and states assumptions instead of interrogating you.

### Outline template
This is the exact structure it emits — reuse it if you want to write or edit an outline by
hand and feed it to the build skill:

```
# <Deck title>
Audience: <who> · Goal: <one takeaway> · Length: <n> slides · Tone: <tone>
Palette: <microsoft | tcs> · Branding: <TCS default | other>

## Slide 1 — <layout type>
Eyebrow: <short mono label>
Heading: <the heading, ≤ 2 lines>
Points:
  - <key point>
  - <key point>
Visual: <animated SVG idea / chart / icon list / none>
Notes: <optional: data to source, tone, transition>

## Slide 2 — <layout type>
...
```

### Layout types (use these names — the build skill implements each)

| Layout | Use for |
|---|---|
| `title` | opening slide |
| `section-divider` | breaking the deck into parts |
| `content-split` | heading + two columns (points beside a visual) |
| `feature-list` | 3–5 parallel points, each an icon + line |
| `big-stat` | one number that carries the slide |
| `bar-chart` / `line-chart` | data shape |
| `quote` | a voice or testimonial |
| `comparison` | A vs B, before/after, options |
| `closing` | takeaway + next step / contact |

Slide-count rule of thumb: ~1–1.5 min per slide (a 15-min talk ≈ 10–14 slides). Default is
10–12 if unspecified.

---

## 4. `deck-build` — the construction pass

### Triggers on
"build the deck", "make the presentation", "create slides", "turn this outline into a deck".

### What it does
Reads the outline (or brief), reads its bundled design references, hand-crafts one
`<section>` per slide, then assembles everything into a single file with:

```bash
python scripts/assemble.py build \
  --slides /home/claude/slides.html \
  --out /mnt/user-data/outputs/<name>.html \
  --title "<Deck title>" \
  --palette microsoft            # or: tcs
```

The assembler inlines the scaffold, the chosen palette, and both theme-swapped logos, and
drops the logo footer onto every slide. Output is one portable `.html`.

### Palettes
- `microsoft` (default) — Fluent azure `#0067C0`, cyan `#00A4EF`, purple `#8661C5`.
- `tcs` — TCS blue `#3D46B0`, violet `#8A5CF0`, magenta `#C74BD1`.

Both are cool, professional, and contain **no orange** by design.

---

## 5. Keyboard controls (built into every deck)

| Key | Action |
|---|---|
| `D` | toggle light / dark theme |
| `F` | fullscreen |
| `E` | export to PDF (rasterised — matches the screen exactly, downloads directly) |
| `P` | print (vector text — one 16:9 page per slide, matches fullscreen) |
| `A` | show / hide the control bar (hidden by default) |
| `←` `→` `Space` | navigate · `Home` / `End` jump to first / last · click screen edges · swipe |

The control bar is hidden until you press `A`, and no shortcut hints appear on the slides —
the deck stays clean for an audience.

**`E` vs `P`:** `E` downloads a pixel-faithful PDF without a dialog but text becomes an image
and it needs internet (it loads two libraries from a CDN). `P` goes through the print dialog,
keeps text crisp and selectable, and works offline. Use `E` for fidelity, `P` for quality
print / selectable text.

---

## 6. Slide templates

These are the building blocks the skill uses. Each is a full `<section>`; the logo footer is
added automatically, so don't include it. Keep the `.frame → .slide-head → .slide-body`
skeleton so the header stays pinned top-left on every slide.

### Title
```html
<section class="slide slide--title">
  <div class="frame">
    <header class="slide-head">
      <span class="eyebrow reveal r1">Eyebrow label</span>
      <h1 class="title reveal r2">Main idea in a<br>few <span class="ink">strong words.</span></h1>
    </header>
    <div class="slide-body">
      <div class="title-cols">
        <div><p class="lead reveal r3">One-sentence subtitle.</p></div>
        <div class="reveal r4"><!-- signature animated SVG --></div>
      </div>
    </div>
  </div>
</section>
```

### Content + visual split
```html
<section class="slide slide--content">
  <div class="frame">
    <header class="slide-head">
      <span class="eyebrow reveal r1">Label</span>
      <h2 class="title reveal r2">Heading here.</h2>
    </header>
    <div class="slide-body">
      <div class="split">
        <div class="reveal r3"><!-- left: prose / list / points --></div>
        <div class="reveal r4"><!-- right: card / chart / diagram --></div>
      </div>
    </div>
  </div>
</section>
```

### Feature / point list (drop inside a column or full width)
```html
<ul class="features">
  <li class="feature reveal r3">
    <span class="ico"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor"
      stroke-width="1.9" stroke-linecap="round" stroke-linejoin="round">
      <!-- lucide-style path --></svg></span>
    <div><h3>Point title</h3><p>One line of explanation.</p></div>
  </li>
  <!-- repeat; bump r4, r5 -->
</ul>
```

### Big stat
```html
<div class="slide-body">
  <div class="split">
    <div class="reveal r3">
      <div style="font-family:var(--font-display);font-weight:700;
        font-size:clamp(3.5rem,9vw,7rem);line-height:.95;letter-spacing:-.03em;
        background:var(--brand-grad);-webkit-background-clip:text;background-clip:text;
        color:transparent">98%</div>
      <p class="lead" style="margin-top:.4em">What the number means.</p>
    </div>
    <div class="reveal r4"><!-- optional supporting visual --></div>
  </div>
</div>
```

### Bar chart (animates on reveal)
```html
<div class="card">
  <div class="card-head"><span class="k">Chart title</span><span class="cap">source</span></div>
  <svg class="chart" viewBox="0 0 360 200" preserveAspectRatio="none">
    <line class="base" x1="8" y1="168" x2="352" y2="168"/>
    <rect class="bar" x="26"  y="82"  width="42" height="86"  rx="6" fill="#0067C0"/>
    <rect class="bar" x="94"  y="54"  width="42" height="114" rx="6" fill="#00A4EF"/>
    <rect class="bar" x="162" y="100" width="42" height="68"  rx="6" fill="#00B7C3"/>
    <rect class="bar" x="230" y="36"  width="42" height="132" rx="6" fill="#107C10"/>
    <rect class="bar" x="298" y="70"  width="42" height="98"  rx="6" fill="#8661C5"/>
    <text class="lbl" x="47" y="186" text-anchor="middle">A</text>
    <!-- one <text> per bar -->
  </svg>
</div>
```

### Quote
```html
<div class="slide-body">
  <blockquote class="reveal r3" style="max-width:22ch;font-family:var(--font-display);
    font-weight:500;font-size:clamp(1.8rem,3.6vw,3rem);line-height:1.2;letter-spacing:-.02em">
    "The sentence that lands." </blockquote>
  <p class="reveal r4" style="font-family:var(--font-mono);font-size:.8rem;color:var(--muted);
    letter-spacing:.1em;margin-top:1.4em">— NAME, ROLE</p>
</div>
```

`section-divider` reuses the title frame without the visual; `comparison` uses `.split` with
a `.card` on each side; `closing` mirrors the title slide with a short sign-off. Full details
and reusable styled classes (`.eyebrow`, `.ink`, `.lead`, `.card`, `.features`, `.chart`) are
in `references/layouts.md` and `references/design-system.md` inside the build skill.

---

## 7. Branding — swapping the logo

The deck ships with your transparent TCS logos (colour on light, white on dark), placed
bottom-right and swapped by theme automatically. To use a different brand, make transparent
versions first, then pass them to the build:

```bash
# colour wordmark on a white background:
python scripts/assemble.py knockout --input raw.png --mode light --out logo_light.png
# white wordmark on a dark background:
python scripts/assemble.py knockout --input raw.png --mode dark  --out logo_dark.png

python scripts/assemble.py build --slides slides.html --out deck.html \
  --logo-light logo_light.png --logo-dark logo_dark.png
```

For the sharpest result, start from an SVG or a high-resolution PNG.

---

## 8. Customizing the look

Everything visual lives in the build skill's bundle:

```
deck-build/
├── SKILL.md                     # the build workflow
├── assets/
│   ├── scaffold.html            # the shell: themes, controls, export, print, header frame
│   ├── logo_light.png           # transparent TCS logo (light theme)
│   └── logo_dark.png            # transparent TCS logo (dark theme)
├── references/
│   ├── design-system.md         # tokens, typography, the header frame, the hard rules
│   └── layouts.md               # the nine slide patterns
└── scripts/
    └── assemble.py              # build + logo-knockout
```

- Change colours globally: edit the `PALETTES` dict in `scripts/assemble.py`, or the token
  blocks — all colour is driven by CSS variables, so one edit re-themes the whole deck.
- Change fonts, spacing, the header frame, or animations: edit `assets/scaffold.html`.
- Add a new slide pattern: add it to `references/layouts.md` so the skill knows to use it.

---

## 9. The hard rules (why decks stay consistent)

Every deck follows these, and they're the first things to check if output ever drifts:

1. The header sits in the **same top-left position on every slide** — never move `.frame`.
2. **No on-screen instructions** or shortcut hints in slide content.
3. **No orange**, anywhere (categorical colours come from the cool set).
4. The **logo is bottom-right on every slide** and swaps by theme.
5. Don't edit the scaffold's `@media print` block or the export JS — they're what make PDF
   and print match the screen.

---

## 10. Troubleshooting

| Symptom | Cause / fix |
|---|---|
| Fonts look generic | The CDN fonts didn't load — open with internet; they fall back to system fonts offline. |
| `E` shows "needs an internet connection" | The export libraries load from a CDN; use `P` offline, or reconnect. |
| PDF colours look washed out on print | Enable "Background graphics" in the browser print dialog (the deck sets `print-color-adjust: exact`, but some browsers still need the toggle). |
| Logo has a faint edge | Source image was low-resolution — re-run `knockout` from a higher-res original or an SVG. |
| A new deck drifts from the house style | Tighten the relevant rule in `references/design-system.md`; the build skill follows those references. |

---

## 11. Tips for the best decks

- Run `deck-outline` first and read the storyboard before building — it's far cheaper to
  fix the story in the outline than in HTML.
- One idea per slide; if a slide needs more than ~5 points or two visuals, split it.
- Keep headings to two lines and let the visuals carry the weight.
- Use real numbers or write "placeholder" — never invent data.
- Alternate dense content slides with lighter dividers, stats, and quotes for rhythm.
