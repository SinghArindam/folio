# Book Reader — Main Design Blueprint
### A static, magazine-styled reading experience for GitHub Pages (`folio`)

---

## Table of Contents

1. Project Vision & Identity
2. Content & File Structure (`content/<book-slug>/` Single-volume & Arc/Volume Support)
3. Explicit File Naming System (`0001-0-ch-1-slug.md`)
4. Technical Stack Decision
5. Visual Design System
6. Themes — Light, Dark, AMOLED
7. Typography System
8. Layout Architecture
9. Navigation & UX Patterns (Collapsible Volume Accordions)
10. Pagination Strategy
11. Micro-interactions Inventory
12. Feature Inventory & Specifications
13. Component Specifications (Sidebar Volume Headers)
14. Responsive Design
15. EPUB Export Specification
16. Performance Strategy
17. GitHub Pages Deployment
18. Summary of Decisions

---

## 1. Project Vision & Identity

A blazing-fast, static web reader for a Markdown-based book. Named **`folio`** (Latin for "leaf" / book format). No server, no database, no framework overhead. The reader lives entirely in the browser — pages load instantly, themes switch without flicker, and reading progress persists between sessions. The aesthetic sits at the intersection of Anthropic's editorial restraint and the tactile warmth of a well-designed print magazine. Every interaction should feel like turning a real page: considered, purposeful, never gratuitous.

**Core principles:**
- Speed over features. If something adds weight without adding clear value, cut it.
- The content is the product. Chrome exists to serve the text, never to compete with it.
- Warm is not loud. Warmth comes from color temperature and typography, not from decoration.
- Dark mode is first-class, not an afterthought. AMOLED specifically targets true black for OLED displays, reducing battery drain and eliminating bloom around text.

---

## 2. Content & File Structure

### Repository layout

The reader is `index.html` — one file, lives at the root. Everything else serves the *content*. `folio` supports both single-volume books and complex multi-volume/arc structures without configuration changes.
Books live in subfolders under `content/<book-slug>/`. Metadata is stored in `meta.md` and master structure in `toc.md`.

```
folio/
│
├── index.html                  ← single-page entry point, standalone reader
├── reader.js                   ← core reader logic
├── reader.css                  ← all styles, including theme vars
│
├── content/
│   └── the-architecture-of-thought/   ← book folder slug
│       ├── meta.md             ← book metadata (title, author, cover, description)
│       ├── toc.md              ← table of contents source of truth
│       │
│       ├── 0000-0-fm-0-dedication.md     ← frontmatter
│       ├── 0000-1-fm-0-preface.md
│       │
│       ├── vol-01-perception/            ← volume subfolder
│       │   ├── 0001-0-ch-1-perception-and-construction.md
│       │   ├── 0002-0-ch-2-language-as-a-mirror.md
│       │   └── ...
│       │
│       ├── vol-02-cognition/             ← volume 2 subfolder
│       │   ├── 0013-0-ch-13-decision-architecture.md
│       │   └── ...
│       │
│       ├── vol-03-synthesis/             ← volume 3 subfolder
│       │   ├── 0025-0-ch-25-creativity-and-divergence.md
│       │   └── ...
│       │
│       └── 9999-0-bm-0-acknowledgements.md ← backmatter
│
└── assets/
    └── images/
        └── shared/             ← book covers and shared figures
```

---
## 3. Explicit File Naming System

Every markdown file follows a strict tokenized naming convention:
`[SEQ:4]-PART-TYPE-NUM-SLUG.md`

### Token Breakdown

| Token | Format | Description | Example |
|---|---|---|---|
| **SEQ** | `0000`–`9999` | 4-digit zero-padded global sorting sequence | `0001` |
| **PART** | Integer | File part index for multi-file split chapters | `0` |
| **TYPE** | String | Section type code (`fm`=frontmatter, `ch`=chapter, `bm`=backmatter) | `ch` |
| **NUM** | Integer | Explicit chapter or section number | `1` |
| **SLUG** | Kebab-case | Human-readable title slug | `perception-and-construction` |

**Full Example:** `0001-0-ch-1-perception-and-construction.md`

---

## 4. Technical Stack Decision

### Recommended: Vanilla HTML + CSS + JavaScript, zero dependencies at runtime

**Why vanilla:**
- GitHub Pages serves static files. Zero build step for basic setup.
- Single `index.html` with inlined CSS and one JS file delivers sub-100ms first paint.
- No heavy frameworks (React/Vue/Svelte).
- `fetch()` API fetches MD files client-side, rendered via `marked.js` (~25KB gzipped).

### Libraries

| Library | Purpose | Size (gzip) | Load Strategy |
|---|---|---|---|
| marked.js | MD → HTML parsing | ~25KB | CDN with local fallback |
| JSZip | EPUB package creation | ~95KB | Lazy-loaded on export demand |

No icon libraries (pure SVG strings). No animation libraries (CSS transitions & `@keyframes`).

---

## 5. Visual Design System

### 4.1 Color Palette

Warm-temperature scale throughout — no cool blues or pure neutrals.

#### Warm Neutral Scale
```
parchment-50:   #FDFAF5   ← lightest surface
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
```
coral-100: #FAEAE2
coral-200: #F5C9AD
coral-300: #E89C72
coral-400: #D87040   ← default accent (light)
coral-500: #C4572A
coral-600: #A03D18   ← hover state
coral-700: #7A2A0C
```

#### Accent Palette — Amber (secondary glow / bookmarks / highlights)
```
amber-100: #FDF3DC
amber-200: #FAD98A
amber-300: #EFB93A
amber-400: #D49B18   ← default amber
amber-500: #A87A0A
```

---

## 6. Themes — Light, Dark, AMOLED

CSS custom property sets applied to `<html>`. Switching themes toggles root class with 200ms transition. Persisted in `localStorage`.

### Light Mode
```
Background (page):        #FAF7F2   ← warm off-white
Background (surface):     #F3EDE0   ← sidebar, top/bottom bar
Background (sidebar):     #EAE2D0
Border:                   rgba(160, 120, 70, 0.16)
Text (primary):           #1E160A   ← warm near-black
Text (secondary):         #6B5438
Accent:                   #C4572A   ← coral-500
```

### Dark Mode
```
Background (page):        #17110A   ← deep warm brown-black
Background (surface):     #1F160C
Background (sidebar):     #130E07
Border:                   rgba(200, 160, 100, 0.12)
Text (primary):           #EAE0D2   ← warm cream
Text (secondary):         #9C7E5C
Accent:                   #E07A4A   ← coral-300
```

### AMOLED Mode
```
Background (page):        #000000   ← absolute black
Background (surface):     #000000
Background (sidebar):     #050505
Border:                   rgba(200, 150, 80, 0.10)
Text (primary):           #F2E8D8
Text (secondary):         #907060
Accent:                   #F08040   ← vivid coral
```

---

## 7. Typography System

### Font Families
1. **Body text:** Lora (Google Fonts) — Serif (Regular 400, Italic 400i, Semibold 600)
2. **Display:** Playfair Display (Google Fonts) — Classical serif (Bold 700 for chapter number display)
3. **UI Chrome:** Inter (Google Fonts) — Sans-serif (Regular 400, Medium 500)

### Type Scale
```
Display (chapter number): 72px / 80px / Playfair Display Bold
H1 (chapter title):       28px / 36px / Lora Semibold
H2 (section heading):     20px / 28px / Lora Semibold
Body:                     17px / 1.85 / Lora Regular
Pull quote:               20px / 1.6 / Lora Italic
Caption:                  13px / 1.5 / Inter Regular, muted
UI label:                 12px / Inter Medium, all-caps + letter-spacing 0.08em
```

---

## 8. Layout Architecture

```
┌─────────────────────────────────────────────────────────┐
│  TOP BAR  (48px fixed height)                           │
│  folio · book title · · · · · font size | A A | theme   │
├────────────────────────────────────────────────────────-┤
│  PROGRESS BAR (2px, coral, full width)                  │
├────────────────┬────────────────────────────────────────┤
│   SIDEBAR      │         CONTENT AREA                   │
│   240px        │         flex: 1                        │
│   [Vol 1 Header]│        max-width: 680px centered      │
│     ch 01      │                                        │
│     ch 02      │                                        │
│   [Vol 2 Header]│                                       │
├────────────────┴────────────────────────────────────────┤
│  BOTTOM NAV  (52px)                                     │
│  ← Previous  ·  Page 3 / 8  ·  ~4 min left  ·  Next →   │
└─────────────────────────────────────────────────────────┘
```

---

## 9. Navigation & UX Patterns

- **Chapter Navigation:** Fetch via `fetch()`, fade content out (150ms), inject HTML, reset scroll, fade in (200ms). Update URL hash `#vol-01/ch-01-perception`.
- **Arc / Volume Accordion:** Clicking Volume/Arc section headers in sidebar collapses or expands that volume's chapter list.
- **Keyboard Navigation:** `Right/L` (next page), `Left/H` (prev page), `N` (next chapter across volume boundaries), `P` (prev chapter), `T` (sidebar), `F` (fullscreen).
- **Touch/Swipe:** Swipe left/right for page navigation; swipe edge right to open sidebar drawer.

---

## 10. Pagination Strategy

Fixed-viewport scroll window (`calc(100vh - 100px)`). Content rendered continuously, revealed through window snap points (`scroll-snap-type: y mandatory`).
Page count = `Math.ceil(contentHeight / viewportHeight)`.

---

## 11. Micro-interactions Inventory

- **Theme toggle:** 200ms color transition with 360° rotating sun/moon icon.
- **Volume expand/collapse:** Accordion header chevron rotates 90° over 150ms on click.
- **Chapter active:** Left border grows from 0 to 100% height (150ms).
- **Chapter complete:** Progress bar under chapter item snap-fills to 100%, checkmark fades in.

---

## 12. Feature Inventory & Specifications

### Core (Launch)
- [x] Markdown rendering (`marked.js`)
- [x] Support for both flat books and Arc / Volume structured books (`arc-`, `vol-`)
- [x] Table of Contents parsed from `content/<book-slug>/toc.md`
- [x] Chapter navigation & URL deep linking (`#vol-01/ch-01`)
- [x] Fixed-viewport paginated reader mode
- [x] Light / Dark / AMOLED themes with localStorage memory
- [x] Reading progress & estimated time remaining
- [x] Responsive layout (drawer sidebar on mobile)

### Enhanced & Innovative
- [x] **EPUB Export:** Client-side generation of downloadable `.epub` ebooks (includes volume grouping).
- [x] **Ambient Chapter Accent:** Per-chapter accent color shift defined in frontmatter (`coral` / `amber` / `teal`).
- [x] **Marginalia & Highlights:** Select text to highlight or add private margin notes saved in localStorage.

---

## 13. Component Specifications

- **Sidebar Volume Headers:** Styled UI group headings representing Arcs (`arc-`) or Volumes (`vol-`). Can be expanded or collapsed to manage long book series.
- **Reading Progress Bar:** 2px bar under top bar showing total book progress. Glows in AMOLED.
- **Chapter Card (sidebar):** Shows chapter number, title, progress bar, optional description subtitle, and checkmark upon completion.
- **Theme Switcher:** Segmented control for Light / Dark / AMOLED.

---

## 14. Responsive Design

- **Mobile (<640px):** Sidebar converts to 80vw drawer overlay. Paginated mode switches to continuous scroll. Top bar condenses to title and hamburger.
- **Tablet (640–1024px):** Sidebar width 200px. Content max-width 560px.
- **Desktop (>1024px):** Full layout, sidebar 240px, content max-width 680px.

---

## 15. EPUB Export Specification

Client-side EPUB generation via `JSZip` (~95KB, loaded dynamically on button click).

### Workflow
1. User clicks "Download EPUB" in settings panel.
2. Reader fetches any unvisited chapters from `toc.md` (resolving relative volume/arc paths).
3. Converts chapter HTML to valid XHTML.
4. Downloads/base64 encodes referenced images into EPUB structure.
5. Generates EPUB metadata (`mimetype`, `container.xml`, `content.opf`, `toc.xhtml`, `styles.css`) dynamically from `toc.md` and `meta.md`, properly nesting volume sections.
6. Packs and triggers browser file download (`folio-book.epub`).

---

## 16. Performance Strategy

- Target first content render <500ms on 4G.
- Total uncached initial payload ~130KB (15KB index.html + 20KB reader.css + 30KB reader.js + 25KB marked.js + 40KB fonts). Warm cache reload ~15KB.

---

## 17. GitHub Pages Deployment

- Hosted static on GitHub Pages.
- Deep links work via hash routes (`https://user.github.io/folio/#vol-01/ch-01`).

---

## 18. Summary of Decisions

### Summary of Decisions

| Decision | Specification |
|---|---|
| Project Name | `folio` |
| Directory Structure | `content/<book-slug>/` (contains `meta.md`, `toc.md`, and volume folders) |
| File Naming Syntax | `0001-0-ch-1-perception-and-construction.md` (`[SEQ:4]-PART-TYPE-NUM-SLUG.md`) |
| Volume / Arc Support | Subfolders (`vol-01-perception/`) with collapsible sidebar accordion UI |
| EPUB Export | Client-side generation preserving volume structure via lazily loaded JSZip |

---

*Document version 3.0 — Comprehensive Main Design Blueprint*
