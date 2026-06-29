# Book Reader — Design Blueprint
### A static, magazine-styled reading experience for GitHub Pages

---

## Table of Contents

1. Project Vision
2. Content & File Structure
3. Technical Stack Decision
4. Visual Design System
5. Themes — Light, Dark, AMOLED
6. Typography System
7. Layout Architecture
8. Navigation & UX Patterns
9. Pagination Strategy
10. Micro-interactions Inventory
11. Feature Inventory
12. Component Specifications
13. Responsive Design
14. Performance Strategy
15. GitHub Pages Deployment
16. Open Questions

---

## 1. Project Vision

A blazing-fast, static web reader for a Markdown-based book. No server, no database, no framework overhead. The reader lives entirely in the browser — pages load instantly, themes switch without flicker, and reading progress persists between sessions. The aesthetic sits at the intersection of Anthropic's editorial restraint and the tactile warmth of a well-designed print magazine. Every interaction should feel like turning a real page: considered, purposeful, never gratuitous.

**Core principles:**
- Speed over features. If something adds weight without adding clear value, cut it.
- The content is the product. Chrome exists to serve the text, never to compete with it.
- Warm is not loud. Warmth comes from color temperature and typography, not from decoration.
- Dark mode is first-class, not an afterthought. AMOLED specifically targets true black for OLED displays, reducing battery drain and eliminating bloom around text.

---

## 2. Content & File Structure

### Repository layout

```
your-book/
├── index.html                  ← single-page entry point, all JS/CSS inlined or linked
├── reader.js                   ← core reader logic
├── reader.css                  ← all styles, including theme vars
│
├── content/
│   ├── table-of-contents.md   ← master ToC, parsed to build sidebar
│   ├── 000-prologue.md
│   ├── 001-chapter-one.md
│   ├── 002-chapter-two.md
│   └── ...
│
├── assets/
│   ├── images/
│   │   ├── chapter-001/       ← images namespaced by chapter to avoid conflicts
│   │   │   ├── fig-01.jpg
│   │   │   └── fig-02.png
│   │   └── chapter-002/
│   └── fonts/                 ← self-hosted fonts (optional, for offline)
│
└── .github/
    └── workflows/
        └── deploy.yml          ← optional: build step if you use a preprocessor
```

### table-of-contents.md format

The ToC file is the single source of truth for the sidebar. The reader parses it to build chapter metadata including display title, slug, and optional description.

```markdown
# The Architecture of Thought

**Author:** Arindam
**Subtitle:** An inquiry into perception, memory, and the stories we tell ourselves

---

## Contents

- [Before We Begin](000-prologue.md) | A few words before the words that matter
- [The First Principles of Perception](001-perception.md) | How the mind constructs the world it inhabits
- [Language as a Mirror](002-language.md) | What we say reveals more than what we mean
- [The Weight of Memory](003-memory.md) | Why the past is never where we left it
- [On Silence and Signal](004-silence.md) | The information contained in what is not said
- [What Remains](005-epilogue.md) | An ending that is also a question
```

The pipe `|` separator after each link is an optional chapter description shown as a subtitle in the sidebar tooltip or expanded state. The reader ignores anything it does not recognise.

### Chapter .md format

Chapters are standard Markdown with a few optional frontmatter fields the reader will use for display. Frontmatter is YAML between `---` fences.

```markdown
---
title: The First Principles of Perception
chapter: 01
subtitle: How the mind constructs the world it inhabits
reading_time: 18
accent: coral          ← optional per-chapter accent tint (coral | amber | teal | default)
---

Body text begins here. The reader treats the first paragraph specially —
it adds a drop cap to the first letter automatically via CSS.

Images work as standard Markdown:

![A diagram showing the visual cortex processing light](../assets/images/chapter-001/fig-01.jpg)
*Figure 1 — The visual cortex devotes more neurons to the fovea than to any other region.*

> Pull quotes are blockquotes. The reader styles them distinctly from
> standard prose blockquotes using a class injected via JS.

Continue normal body text here.
```

### Image handling notes

- Images use relative paths from the chapter file: `../assets/images/chapter-XXX/filename.ext`
- The reader lazy-loads all images using the native `loading="lazy"` attribute injected during MD render
- Images get a subtle fade-in on load to avoid layout pop
- Captions: any `*italic text*` immediately following an image (no blank line) is treated as a figure caption and styled distinctly
- Wide images (landscape) get a full-bleed treatment on desktop; they stretch past the text column to the content area edge
- Portrait images stay constrained to the text column width

---

## 3. Technical Stack Decision

### Recommended: Vanilla HTML + CSS + JavaScript, zero dependencies at runtime

**Why vanilla:**
- GitHub Pages serves static files. No build step required for basic setup.
- A single `index.html` with inlined CSS and one JS file delivers sub-100ms first paint.
- No React, no Vue, no Svelte — these add hundreds of kilobytes for a problem that does not require a component framework.
- The reader fetches MD files via the `fetch()` API and renders them client-side using one small library (marked.js, ~25KB gzipped).

**The only runtime dependency:** `marked.js` for Markdown parsing, loaded from a CDN with a local fallback copy. Everything else is hand-written.

**Optional enhancement (if you want a build step):** Use Eleventy (11ty) to pre-render all MD files to HTML at build time. This removes the client-side MD parsing entirely and makes the reader even faster. The trade-off is a small GitHub Actions workflow. Recommended if the book grows beyond ~20 chapters.

### Libraries

| Library | Purpose | Size (gzip) | CDN |
|---|---|---|---|
| marked.js | MD → HTML parsing | ~25KB | jsDelivr |
| none else | — | — | — |

No icon library needed — icons are pure SVG strings inlined in JS. No animation library needed — all transitions use CSS `transition` and `@keyframes`.

---

## 4. Visual Design System

### 4.1 Color Palette

The palette is warm-temperature throughout — no cool blues or pure neutrals. Even the "gray" tones carry a slight amber cast. This creates cohesion across all three themes.

#### Warm Neutral Scale (used across all themes)

Named from lightest to darkest. Every theme draws from this scale for surfaces, text, and borders.

```
parchment-50:   #FDFAF5   ← lightest surface, almost white
parchment-100:  #F7F2E8
parchment-200:  #EDE6D6
parchment-300:  #DDD4C0
parchment-400:  #C8BA9E
parchment-500:  #AA9070
parchment-600:  #8A7050
parchment-700:  #6A5038
parchment-800:  #3D2E1E
parchment-900:  #211508   ← richest dark, near-black warm
```

#### Accent Palette — Coral (primary accent)

The main interactive color. Used for: active chapter highlight, drop caps, progress bar, hover states, pull quote border, links.

```
coral-100: #FAEAE2
coral-200: #F5C9AD
coral-300: #E89C72
coral-400: #D87040   ← default accent (light mode)
coral-500: #C4572A
coral-600: #A03D18   ← hover state
coral-700: #7A2A0C
```

#### Accent Palette — Amber (secondary, warm glow)

Used for: reading progress glow on AMOLED, bookmarks, starred highlights, special callouts.

```
amber-100: #FDF3DC
amber-200: #FAD98A
amber-300: #EFB93A
amber-400: #D49B18   ← default amber
amber-500: #A87A0A
```

#### Semantic Colors

```
success: #5A8A5A   ← chapter complete checkmark
muted:   parchment-500 level in current theme
```

### 4.2 Spacing Scale

The system uses a base-8 spacing scale. Everything is a multiple of 4px, with preferred steps at 8, 16, 24, 32, 48, 64, 96.

```
--space-1:   4px
--space-2:   8px
--space-3:   12px
--space-4:   16px
--space-5:   24px
--space-6:   32px
--space-7:   48px
--space-8:   64px
--space-9:   96px
--space-10:  128px
```

### 4.3 Border Radius

```
--radius-sm:  4px    ← small tags, badges
--radius-md:  8px    ← buttons, inputs
--radius-lg:  12px   ← cards, sidebar panels
--radius-xl:  16px   ← modal sheets
```

### 4.4 Shadows

Shadows are warm-tinted and extremely subtle. Only used for the sidebar on mobile overlay mode.

```
--shadow-sidebar: 4px 0 24px rgba(60, 30, 10, 0.12)
```

No shadows on content elements. Separation is handled by color differentiation, not elevation.

---

## 5. Themes — Light, Dark, AMOLED

All three themes are defined as CSS custom property sets applied to the root element. Switching themes is a single class toggle on `<html>` with a 200ms transition on all color properties. Theme preference is saved to `localStorage` and applied before first paint to prevent flash.

### Light Mode

Evokes warm afternoon light through paper. High contrast, comfortable for long reading sessions.

```
Background (page):        #FAF7F2   ← warm off-white, never pure white
Background (surface):     #F3EDE0   ← sidebar, top bar, bottom bar
Background (sidebar):     #EAE2D0   ← slightly deeper warm tone
Border:                   rgba(160, 120, 70, 0.16)

Text (primary):           #1E160A   ← warm near-black
Text (secondary):         #6B5438   ← warm brown-gray
Text (muted):             #9C7E5C   ← light labels, captions

Accent:                   #C4572A   ← coral-500
Accent background:        #FAEAE2   ← coral-100, for highlights and hover

Sidebar active:           border-left coral-500, background coral-100
Progress bar:             coral-500
Drop cap:                 coral-500
Pull quote border:        coral-400
```

### Dark Mode

Rich, warm darkness. Not black — more like candlelit room. Amber and coral glow naturally against the deep brown-black backgrounds.

```
Background (page):        #17110A   ← very deep warm brown-black
Background (surface):     #1F160C   ← slightly lighter for bars
Background (sidebar):     #130E07   ← deepest, recedes visually
Border:                   rgba(200, 160, 100, 0.12)

Text (primary):           #EAE0D2   ← warm cream, not harsh white
Text (secondary):         #9C7E5C   ← warm mid-tone
Text (muted):             #6B5438   ← for labels and captions

Accent:                   #E07A4A   ← coral-300, brighter for dark context
Accent background:        #3D2010   ← very dark coral tint
```

### AMOLED Mode

True black backgrounds for OLED displays. Zero bloom. Accent colors pop intensely. Every surface that would be a dark gray in Dark mode becomes #000000. This saves meaningful battery on OLED screens and creates a sharp, almost print-like contrast.

```
Background (page):        #000000   ← absolute black
Background (surface):     #000000   ← also absolute black (no distinction needed)
Background (sidebar):     #050505   ← near-black, just enough to show edge

Border:                   rgba(200, 150, 80, 0.10)   ← barely visible warm line

Text (primary):           #F2E8D8   ← bright warm cream, max contrast
Text (secondary):         #907060   ← warm mid-tone
Text (muted):             #604830   ← for labels

Accent:                   #F08040   ← warm coral, vivid on black
Accent background:        #1A0800   ← very dark near-black tint
```

### AMOLED special behaviors

- The top bar and bottom bar become transparent (no surface color) — only a hairline border separates them from the black page
- The sidebar draws a very faint border on its right edge instead of a background color shift
- The reading progress bar glows — a very subtle 2px amber blur is the only shadow in the entire system, and it only exists in AMOLED mode
- The drop cap pulses with a single very brief glow on chapter load (one-time, not looping)

---

## 6. Typography System

Typography is the soul of this project. Every measurement here is deliberate.

### Font Families

Three families, each with a distinct purpose:

**1. Body text — Lora (Google Fonts)**
A contemporary serif inspired by classical typefaces. Warm, literary, works at small sizes on screens. Used for all chapter prose, pull quotes, chapter subtitles.
- Regular (400): prose
- Regular Italic (400i): emphasis, pull quotes, captions
- Semibold (600): chapter titles, frontmatter headings

**2. Display — Playfair Display (Google Fonts)**
High-contrast classical serif with strong ink-trap characteristics. Used exclusively for the large chapter number display and the book title in the top bar. Its dramatic thick-thin contrast signals "this is a book."
- Bold (700): chapter number display (the very large numeral on chapter open)

**3. UI Chrome — Inter (Google Fonts)**
Clean, neutral grotesque. Used for all navigation, labels, buttons, progress indicators, settings. Never appears in the actual reading area.
- Regular (400): labels, body
- Medium (500): buttons, active states

### Type Scale

```
Display (chapter number):   72px / 80px line-height / Playfair Display Bold
H1 (chapter title):         28px / 36px line-height / Lora Semibold
H2 (section heading):       20px / 28px line-height / Lora Semibold
H3 (subsection):            17px / 24px line-height / Lora Semibold
Body:                       17px / 1.85 line-height / Lora Regular
Pull quote:                 20px / 1.6 line-height  / Lora Italic
Caption:                    13px / 1.5 line-height  / Inter Regular, muted color
UI label:                   12px / Inter Medium, all-caps + letter-spacing 0.08em
UI body:                    13px / Inter Regular
UI small:                   11px / Inter Regular, muted
```

### Drop Cap

The first letter of the first paragraph of every chapter is a drop cap. It:
- Uses Playfair Display Bold
- Floats left at approximately 3.5× the body size
- Is colored with the accent (coral)
- Occupies exactly 3 lines of text height
- Has 8px right margin before the following text
- Has no extra top margin — it optically aligns with the cap height of the first line

Drop caps are applied via CSS `::first-letter` on a `.chapter-opening-paragraph` class injected by the MD renderer.

### Pull Quotes

Blockquotes in Markdown that begin with a quotation mark character are styled as pull quotes. All others are styled as standard block quotes (indented, smaller, muted).

Pull quote styling:
- Full text column width (no indent)
- 2px solid left border in accent color
- 24px left padding
- Lora Italic at 20px
- Muted text color
- 32px top and bottom margin
- No background, no border radius

### Running Headers

On desktop, a very subtle running header is painted at the top of the content area (not the top bar — the top bar has the book title). It shows:
- Left: book title, small, muted
- Right: current chapter title, small, muted

These fade in as you scroll past the first paragraph (so they don't overlap the chapter header).

### Image Captions

Any `*italic*` text immediately following an image in the MD source (no blank line between) is treated as a figure caption. Caption styling:
- Inter Regular 13px
- Muted text color
- Center-aligned
- 12px margin above (between image and caption)
- 28px margin below (separating caption from next paragraph)
- Optional: an automatic figure number (Figure 1, Figure 2, etc.) prepended, depending on user preference set in frontmatter

---

## 7. Layout Architecture

### Overall structure

```
┌─────────────────────────────────────────────────────────┐
│  TOP BAR  (48px fixed height)                           │
│  book title · · · · · · · · · · font size | A A | theme │
├────────────────────────────────────────────────────────-┤
│  PROGRESS BAR (2px, coral, spans full width)            │
├────────────────┬────────────────────────────────────────┤
│                │                                        │
│   SIDEBAR      │         CONTENT AREA                   │
│   240px        │         flex: 1                        │
│                │                                        │
│  ┌──────────┐  │  ┌──────────────────────────────────┐  │
│  │ TOC      │  │  │  CHAPTER HEADER                  │  │
│  │          │  │  │  running header (fades in)       │  │
│  │  ch 001  │  │  │                                  │  │
│  │  ch 002  │  │  │  READING AREA (scrollable)       │  │
│  │  ch 003  │  │  │  max-width: 680px centered       │  │
│  │  ...     │  │  │                                  │  │
│  └──────────┘  │  └──────────────────────────────────┘  │
│                │                                        │
├────────────────┴────────────────────────────────────────┤
│  BOTTOM NAV  (52px)                                     │
│  ← Previous  ·  Page 3 / 8  ·  ~4 min left  ·  Next → │
└─────────────────────────────────────────────────────────┘
```

### Top Bar

Height: 48px. Sticky, never scrolls.

Left cluster:
- Hamburger icon (mobile) or book mark icon (desktop): 20px SVG
- Book title: Playfair Display, 14px, warm primary color
- Divider: 1px vertical, 16px tall, muted
- Chapter title (current): Inter, 13px, muted color

Right cluster:
- Font size controls: two buttons labeled "A" (smaller) and "A" (larger), Inter
- Divider
- Theme toggle: three-state button — sun icon (light) / moon icon (dark) / AMOLED text

The top bar background uses the surface color, not the page background. A 0.5px bottom border in the warm border color.

### Sidebar / Table of Contents

Width: 240px on desktop. On tablet (768–1024px): 200px. On mobile: overlays as a drawer from the left, full height, 80vw wide.

Sidebar sections:
1. Sidebar header (40px) — "CONTENTS" label, Inter 10px, all-caps, letter-spacing
2. Scrollable chapter list (flex: 1, overflow-y: auto)
3. Optional: reading stats footer (24px) — "X of N chapters complete"

Each chapter item in the list:
- Padding: 12px 16px
- Chapter number: Inter 10px, all-caps, muted (CHAPTER 01 / PROLOGUE / EPILOGUE)
- Chapter title: Inter 13px, primary text
- Chapter description (optional): Inter 11px, muted, 2 lines max, hidden until hover on desktop
- Progress bar: 2px tall, full item width, shows reading progress in that chapter
- Active state: 2px left border in coral, background tint of coral-100 (or equivalent in dark themes)
- Hover state: background tint, no border change until active
- Complete state: small checkmark icon (12px, success green) next to chapter number

Sidebar scroll: the sidebar scrolls independently from the content area. Active chapter is automatically scrolled into view when chapters are switched.

### Content Area

The main reading area. Flexes to fill remaining width after sidebar.

Inside the content area, text is constrained to a max-width of 680px and centered. This keeps line lengths comfortable at ~65–75 characters per line on desktop, which is the sweet spot for sustained reading.

The content area itself scrolls. This is where pagination lives (see Section 9).

Content area padding:
- Top: 48px (generous breathing room before chapter header)
- Bottom: 64px
- Left/Right: clamp(24px, 5vw, 64px) — responsive, tighter on small screens

### Bottom Navigation Bar

Height: 52px. Sticky at bottom.

Left: ← Previous chapter button (only if not on first chapter)
Center:
- Page indicator: "Page 3 of 8" in Inter 12px, muted
- Below that (very small, 11px): "~4 min remaining in chapter" — calculated from word count and reading speed setting
Right: Next chapter button →

On the last page of a chapter, the Next button reads "Next chapter →" and navigates to the first page of the following chapter.

On the last page of the last chapter, the Next button is replaced with "The End" (no button, just text in Lora Italic, muted).

---

## 8. Navigation & UX Patterns

### Chapter Navigation

Chapters are loaded dynamically via `fetch()`. When a chapter link is clicked:
1. Content area fades out (150ms)
2. New chapter MD is fetched and parsed
3. Content is injected, scroll position reset to 0
4. Content area fades in (200ms)
5. URL hash updates to `#chapter-001` (enables deep linking)
6. Reading position for the previous chapter is saved to localStorage
7. Active state in sidebar updates

The fade transition is instant enough to feel responsive but present enough to signal "you moved."

### Within-Chapter Page Navigation

See Section 9 (Pagination Strategy) for the technical approach.

Navigating pages within a chapter: no fade. The scroll animation is a smooth scroll (300ms, ease-in-out) to the next "page" position. This feels like physically turning a page — faster and more tactile than chapter transitions.

### Reading Progress

Progress is tracked at two levels:
1. Chapter-level: what fraction of the chapter's scroll height have you passed? Saved to localStorage per chapter.
2. Overall book progress: the top progress bar shows (chapters fully read + fraction of current chapter) / total chapters as a percentage.

On return to the reader, the previously-read position is restored automatically. A small "Continue from where you left off" notification can appear in the top bar for the first few seconds after load — clicking it jumps to the saved position.

### Keyboard Navigation

```
Arrow Right / L    → Next page (within chapter)
Arrow Left  / H    → Previous page (within chapter)
N                  → Next chapter
P                  → Previous chapter
T                  → Toggle sidebar
F                  → Toggle fullscreen reading mode (hide top bar and sidebar)
Cmd/Ctrl + K       → Search (if implemented)
Escape             → Close sidebar (mobile), exit fullscreen mode
```

Keyboard navigation is announced for screen readers via `aria-live` regions.

### Touch & Swipe (Mobile)

- Swipe left → next page
- Swipe right → previous page
- Swipe right from left edge (within 40px) → open sidebar
- Swipe left on sidebar → close sidebar

Swipe detection uses vanilla JS touch events (touchstart / touchend). The threshold is 60px displacement. Velocity is checked — fast flicks register even if they do not hit 60px.

### Bookmark System

Tapping the bookmark icon (top right area of content) saves the current chapter + page to localStorage. The bookmark appears as a small ribbon in the sidebar next to the chapter title. Multiple bookmarks allowed — accessing the bookmark list shows them in a small popover.

---

## 9. Pagination Strategy

This is the most architecturally significant decision in the reader.

### Recommended approach: Fixed-viewport scroll window

The content area has a fixed height (viewport height minus the top bar and bottom bar combined: `calc(100vh - 100px)`). Content inside it is rendered as a single continuous block but is revealed through this fixed window.

"Pages" are scroll positions, not separate DOM elements. One page = one viewport-height of content.

How it works:
1. After MD is rendered to HTML, the full content height is measured.
2. Page count = `Math.ceil(contentHeight / viewportHeight)`.
3. "Next page" smooth-scrolls the container by exactly `viewportHeight`.
4. Current page = `Math.floor(scrollTop / viewportHeight) + 1`.

This approach:
- Works perfectly with images (no content splitting)
- Requires zero JS to split or restructure the MD
- Pages feel natural — you never cut mid-sentence
- Is accurate to within a few pixels

The one limitation: on very small screens or with large images, a single image might fill more than one page. This is acceptable — it mirrors real book behavior.

### Desktop vs Mobile

Desktop (≥768px): Paginated mode is the default. Content area does not show a scrollbar. Navigation is prev/next page buttons + keyboard arrows.

Mobile (<768px): Continuous scroll is the default. The page indicator in the bottom bar still shows progress ("35% through chapter") but next/prev buttons navigate chapters, not pages.

Users can override: a toggle in settings switches between "Page mode" and "Scroll mode" on any device.

### Page transition animation

Between pages: `scroll-behavior: smooth` on the content container with `scroll-snap-type: y mandatory`. Each page's starting position is a snap point. This gives a clean, controlled snap on scroll as well as programmatic navigation.

On a fast enough device, you can add a subtle overlay that flashes briefly (very short opacity 0→0.06→0 over 150ms) at the moment of snap, which simulates the blink of turning a page. This can be disabled in settings.

---

## 10. Micro-interactions Inventory

These are all the small moments of delight or feedback. Each should be fast (under 250ms) and purposeful.

### Content interactions

**Chapter load:** content area fades from 0 to 1 opacity over 200ms. The chapter header slides up 8px while fading in — a gentle reveal.

**Drop cap on load:** the drop cap letter does a single very brief weight pulse (font-weight 600→700→600 over 300ms) on AMOLED only. On other themes it simply appears.

**Page turn:** smooth scroll snap. On desktop, a near-invisible flash overlay signals the page boundary.

**Image load:** images fade in from opacity 0 to 1 over 200ms after the `load` event. No layout shift because the aspect ratio is preserved with a skeleton placeholder.

**Hover on chapter list item:** background tint transitions in over 100ms. No jump, no border change.

**Active chapter in sidebar:** a 2px left border grows from 0 height to full height over 150ms when a chapter becomes active.

**Chapter complete (chapter progress hits 100%):** the small progress bar under the chapter item in the sidebar transitions to full width with a slight overshoot (it goes to 105% then back to 100%) giving a satisfying "snap to complete" feel. A checkmark fades in.

### Control interactions

**Theme toggle:** all background colors and text colors transition simultaneously over 200ms. The sun/moon icon rotates 360° as it transitions (a single rotation, not a loop). The page does not flicker because the theme class is applied before the transition classes are added.

**Font size buttons:** content text-size transitions over 150ms. The line heights reflow — the page count may change, which is updated immediately.

**Progress bar:** updates continuously as you scroll. Uses `requestAnimationFrame` throttling so it does not block.

**Sidebar open/close (mobile):** drawer slides in/out from the left over 250ms with a cubic-bezier(0.4, 0, 0.2, 1) easing. An overlay appears behind it at 50% opacity.

**Next/Prev buttons:** on hover, the arrow icon translates 3px in the direction of travel (right arrow moves right, left arrow moves left). On click, it translates an additional 2px and scales very slightly.

**Top bar on scroll:** when you begin reading (scroll past the chapter header), the book title in the top bar transitions to showing the chapter title instead. A very short cross-fade (150ms) makes this seamless. Scrolling back up reverses it.

**Reading progress bar:** the 2px coral bar at the top of the page grows as you read. It is a single continuous bar representing total book progress (not just chapter progress), so it grows very slowly as you read. Satisfying in the long run.

---

## 11. Feature Inventory

### Core (must have at launch)

- [ ] Markdown rendering with `marked.js`
- [ ] Image support with lazy loading and aspect-ratio preservation
- [ ] Table of Contents parsed from `table-of-contents.md`
- [ ] Chapter-by-chapter navigation with fetch + fade
- [ ] Paginated content area (fixed-viewport scroll window)
- [ ] Page indicator (Page N of M) and chapter reading time estimate
- [ ] Three themes: Light / Dark / AMOLED with localStorage persistence
- [ ] Reading progress saved per chapter in localStorage
- [ ] Font size control (three steps: small / medium / large)
- [ ] Keyboard navigation (arrows, N/P for chapters)
- [ ] Deep linking via URL hash (`#chapter-001`, `#chapter-001/page-3`)
- [ ] Mobile-responsive layout with drawer sidebar
- [ ] Touch swipe navigation
- [ ] Running header (book title + chapter title, fades in after chapter header)

### Enhanced (add in passes after core is solid)

- [ ] Bookmarks — save any chapter/page, list of bookmarks per book
- [ ] Reading speed calibration — the user sets their WPM once, all time estimates use it
- [ ] Full-screen / Focus mode — hide sidebar and controls for distraction-free reading
- [ ] Print mode — a clean CSS print stylesheet that formats every chapter as a beautifully typeset printed page
- [ ] Font family toggle — offer two body fonts (Lora as default, a sans-serif like Source Sans as alternative)
- [ ] "Continue reading" toast on page load — shows the last read chapter and page with a button to jump there
- [ ] Scroll mode vs Page mode toggle in settings
- [ ] Night Shift — automatically switch to Dark or AMOLED after sunset, based on time (user's local time)

### Innovative (differentiated features worth planning around)

- [ ] **Ambient chapter accent** — each chapter has an optional accent color in its frontmatter (coral / amber / teal). The entire reader subtly shifts its accent to match: the progress bar, drop cap, active sidebar border, and pull quote border all shift hue together over 400ms when a new chapter loads. Gives each chapter its own emotional temperature.

- [ ] **Marginalia** — select any word or sentence and press M. A small text input appears in the right margin (or in a bottom panel on mobile) where you type a private note. Notes are saved in localStorage keyed to chapter + character offset. They persist between sessions and show as small amber dots in the content margin. Hovering a dot reveals the note. This turns your reader into a personal annotated copy.

- [ ] **Highlight system** — select text, a small toolbar appears (similar to Kindle) offering: Highlight (saves the selection with a colored underline, stored in localStorage), Add note, Copy. Three highlight colors: amber, coral, teal. Reading back through a highlighted chapter shows all your markings.

- [ ] **Reading heatmap in sidebar** — each chapter item in the sidebar has a micro-visualization (a tiny bar or sparkline, ~20px wide) showing reading density. Pages you re-read multiple times (scroll back through) appear darker. This reflects genuine reading behavior — you linger on some sections.

- [ ] **Chapter illustrations / cover images** — if the chapter frontmatter includes a `cover_image` path, an image is displayed as a full-bleed header behind the chapter title. The title text becomes white with a dark gradient overlay. On scroll, the cover image parallax-scrolls at 0.5× speed, creating depth. A purely visual enhancement.

- [ ] **Table of Contents with reading time badges** — each chapter item in the sidebar shows a tiny "~12 min" badge. As the user reads, the badge updates to show remaining time in that chapter if they have already started it ("~4 min left").

- [ ] **Continuous reading stitching** — when you reach the last page of a chapter, a preview of the next chapter's first paragraph fades in below a thin divider at the bottom of the viewport. Clicking it continues into the next chapter seamlessly without navigating away. This makes the book feel like one continuous document rather than segmented files.

- [ ] **Read-aloud mode (optional, explicit opt-in)** — uses the browser's built-in `SpeechSynthesis` API to read the current page aloud, highlighting each sentence as it is spoken. No server, no API key — 100% browser native. Works offline.

---

## 12. Component Specifications

### Reading Progress Bar

- Position: directly below the top bar, full viewport width
- Height: 2px
- Background: transparent (you see the surface color behind)
- Fill: coral accent color (theme-appropriate)
- Transition: width updates continuously via scroll event with `requestAnimationFrame`
- In AMOLED: a 2px coral glow radiates below the bar (the only glow/shadow in the system)
- Represents total book progress (not just chapter), so it moves very slowly

### Chapter Card (in sidebar)

Dimensions: full sidebar width × variable height (min ~56px)
Padding: 12px 16px
Sections (top to bottom):
- Chapter label row: chapter number ("CHAPTER 01") in Inter 10px, all-caps, letter-spacing, muted
- Chapter title: Inter 13px, primary text, 2 lines max
- Progress bar: 2px high, full width, warm border color as background, coral fill at X%
- Description: Inter 11px, muted, max 2 lines — hidden by default, shown on hover (desktop) or always shown if sidebar is expanded

State styling:
- Default: no border, default surface background
- Hover: 100ms background tint transition
- Active: 2px left border coral, coral tint background
- Complete: chapter label row gets a small ✓ icon (12px, success green) prepended

### Theme Switcher

Three states in a single button group (segmented control).
- Each segment: icon or label, 32px × 32px touch target
- Segments: ☀ (light) / ☾ (dark) / AMOLED
- Active segment: filled background in accent color, inverted label color
- Transition: 200ms cross-fade between states
- Entire control width: ~120px

### Font Size Control

Two buttons: A (small) and A (large). Repeated press moves through three steps.
Steps: 15px / 17px / 20px body text. Line heights scale proportionally.
Active step is visually indicated by the button's filled state.
Transition on resize: 150ms transition on `font-size` and `line-height`.

### Settings Panel

A bottom sheet on mobile, a popover on desktop.
Settings available:
- Theme (duplicates the top bar control for convenience)
- Font family (Lora / Sans alternative)
- Font size (slider or step buttons)
- Line spacing (compact / normal / relaxed)
- Page mode vs Scroll mode
- Reading speed (WPM, for time estimates)
- Night Shift on/off
- Marginalia on/off

All settings saved immediately to localStorage on change.

---

## 13. Responsive Design

### Breakpoints

```
mobile:         < 640px
tablet:         640px – 1024px
desktop:        > 1024px
wide:           > 1440px
```

### Mobile (<640px)

- Sidebar is hidden, accessible via hamburger button in top bar
- Sidebar opens as a left drawer overlay (80vw, full height)
- Pagination becomes continuous scroll (scroll mode default)
- Bottom bar shows chapter progress instead of page number
- Content padding: 20px horizontal
- Body text: 16px (slightly smaller to fit more per screen)
- Top bar shows only book title and hamburger — other controls move into settings panel
- Drop cap is smaller (2.5× body size instead of 3.5×)

### Tablet (640–1024px)

- Sidebar is shown at 200px (narrower than desktop)
- Chapter descriptions in sidebar are hidden
- Body text column: max-width 560px
- Pagination: page mode enabled but with smaller "pages" (tablet viewport height)
- Top bar: all controls visible

### Desktop (>1024px)

- Full layout as specified in Section 7
- Sidebar: 240px
- Content max-width: 680px centered in remaining space
- Pull quotes may optionally extend slightly into the margin (bleed beyond text column by ~40px) for a magazine-style feel

### Wide (>1440px)

- Consider a three-column layout: sidebar / content / optional margin column for marginalia notes
- Or simply increase content max-width to 720px and add wider padding

---

## 14. Performance Strategy

**Goal: First content render under 500ms on a 4G connection. Interactive under 1 second.**

### Loading strategy

1. `index.html` is a single file with inlined critical CSS (the above-the-fold styles: top bar, sidebar shell, content area placeholder).
2. `reader.css` (full styles) is loaded asynchronously via `<link rel="preload">`.
3. `reader.js` is a single non-blocking `<script defer>`.
4. Google Fonts are loaded with `font-display: swap` so text shows immediately in system serif while the web font loads, then swaps in without layout shift.
5. `marked.js` is loaded from a CDN with a local copy as fallback.

### Fetch and cache strategy

All chapter MD files are fetched with `Cache-Control: max-age=3600` (1 hour). Once fetched, they are stored in a module-level JS Map so revisiting a chapter within a session does not re-fetch.

On GitHub Pages, assets are served by GitHub's CDN, which is globally distributed. Latency for returning visitors with warm cache: effectively zero.

### Image performance

- All images in the content area: `loading="lazy"` and `decoding="async"` injected during MD render
- Intrinsic dimensions specified in frontmatter or inferred from image file dimensions at build time (if a build step is used)
- If no dimensions available, the image container gets a 16:9 aspect ratio placeholder that prevents layout shift
- No external image services, no image CDN — just files in `assets/images/`

### JS performance

- No framework, no virtual DOM, no reconciliation
- Event delegation for chapter link clicks (one event listener on the sidebar, not one per item)
- Scroll event listeners are passive and debounced
- The page count calculation happens once on chapter load, not on every scroll
- Theme switching is a single classList change on `<html>` — no re-render

### Total page weight target

```
index.html (with inlined critical CSS):    ~15KB
reader.css:                                ~20KB
reader.js:                                 ~30KB
marked.js (CDN, cached after first visit): ~25KB gzip
Fonts (Lora subset + Inter subset):        ~40KB combined, cached aggressively
─────────────────────────────────────────────────
Total first load (no cache):               ~130KB
Total subsequent loads (warm cache):       ~15KB
```

This is orders of magnitude lighter than any framework-based approach.

---

## 15. GitHub Pages Deployment

### Basic setup (no build step)

1. Push repo to GitHub
2. Go to Settings → Pages → Source: main branch, root `/`
3. GitHub Pages serves `index.html` at `https://username.github.io/repo-name/`
4. All chapter MD files in `content/` are served as static files — the reader fetches them via relative URLs

**Important:** Chapter fetches use relative paths (`./content/001-chapter-one.md`), so they work on any domain without configuration.

### Custom domain setup

If you want `book.yourdomain.com`:
1. Add a `CNAME` file in the root with `book.yourdomain.com`
2. Set a CNAME DNS record at your registrar pointing to `username.github.io`
3. Enable HTTPS in GitHub Pages settings (automatic, via Let's Encrypt)

### Optional build step with Eleventy (recommended for larger books)

If the book grows beyond ~15 chapters, pre-building HTML from MD eliminates client-side parsing entirely:

1. Install Eleventy: `npm install --save-dev @11ty/eleventy`
2. Configure it to treat `content/*.md` as pages
3. GitHub Actions workflow:
   - Trigger on push to main
   - Run `npx eleventy`
   - Deploy `_site/` folder to GitHub Pages
4. Result: every chapter is already HTML when the browser requests it — no JS parsing needed

### URL structure

Without build step:  `https://domain.com/#chapter-001` (hash-based routing)
With Eleventy:       `https://domain.com/chapter/001/` (real paths, prettier)

Hash-based routing works perfectly for GitHub Pages without a build step and preserves deep link sharing.

---

## 16. Open Questions

These are design and content decisions that need your input before work begins.

**About the book:**
1. What is the book about? Fiction, non-fiction, technical, personal essays? This affects typography decisions (e.g., technical content may need code block styling) and whether chapter illustrations make sense.
2. How long is a typical chapter? (Word count range.) This determines whether pagination is important or whether chapters are short enough to fit in one or two pages naturally.
3. Do you have a title and subtitle for the book?
4. Will there be images in chapters? If so, are they decorative illustrations or informational diagrams?

**About design preferences:**
5. Do you want a cover page / splash screen before the reader, or do you go straight to Chapter 1 (or the ToC)?
6. Chapter accent colors — do you want each chapter to have its own color, or a unified palette throughout?
7. Pull quotes — do you plan to write pull quotes intentionally in your MD, or should the reader automatically extract a notable sentence from each chapter?
8. Should the book have a visible author name / about page?

**About features:**
9. Marginalia and highlights — are these important to you, or would you prefer to keep the reader simpler and not have annotation features?
10. Do you want a search feature? (Adds complexity — requires either a pre-built search index or scanning all chapter files.)
11. Should the reader support multiple books eventually, or is this always one book per deployment?
12. Do you want offline support / PWA installability? This requires a service worker (~50 additional lines of JS) but allows readers to save the book for offline reading.

**About technical setup:**
13. Are you comfortable with a minimal build step (GitHub Actions + Eleventy), or do you want zero build steps — pure static files that work by just pushing to GitHub?
14. Do you want the reader available at the GitHub Pages URL (free), or do you have a custom domain in mind?

---

*Document version 1.0 — brainstorming / pre-development phase*
*Created as a design blueprint for iterative development starting from core features*
