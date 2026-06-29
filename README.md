# folio

A blazing-fast, static book reader for Markdown content. No server, no database, no framework. Pure HTML + CSS + JS. Hosted on GitHub Pages.

---

## Features

- **Markdown-native** — Write chapters in `.md`, reader renders client-side via `marked.js`
- **Three themes** — Light, Dark, AMOLED (true black for OLED). Persisted in localStorage
- **Volume / Arc support** — Flat books or nested `vol-`/`arc-` subfolder structures
- **Collapsible sidebar** — Volume accordion headers, active chapter highlighting
- **Typography** — Lora (body), Playfair Display (display), Inter (UI). Drop caps on chapter opens
- **Reading progress** — 2px coral progress bar tracks position across entire book
- **Keyboard navigation** — `←/→` page scroll, `N/P` chapter nav, `T` sidebar toggle
- **Touch/swipe** — Swipe from edge opens sidebar on mobile
- **Deep linking** — Hash-based URLs (`#vol-01-perception/0001-0-ch-1-perception.md`)
- **Font size control** — 8 steps from 15px to 24px
- **Responsive** — Mobile drawer sidebar, tablet compact, desktop full layout
- **Zero dependencies at runtime** — Only `marked.js` (~25KB) from CDN

---

## File Naming Convention

Every content file follows: `[SEQ:4]-PART-TYPE-NUM-SLUG.md`

| Token | Format | Purpose |
|---|---|---|
| SEQ | `0000`–`9999` | Global sort order |
| PART | Integer | Multi-file chapter split index |
| TYPE | `fm`/`ch`/`bm` | Frontmatter, chapter, or backmatter |
| NUM | Integer | Chapter number |
| SLUG | Kebab-case | Human-readable title |

**Example:** `0001-0-ch-1-perception-and-construction.md`

---

## Project Structure

```
folio/
├── index.html                              ← standalone reader
├── content/
│   └── the-architecture-of-thought/        ← book folder
│       ├── meta.md                         ← book metadata
│       ├── toc.md                          ← table of contents (source of truth)
│       ├── 0000-0-fm-0-dedication.md       ← frontmatter
│       ├── vol-01-perception/              ← volume subfolder
│       │   ├── 0001-0-ch-1-perception-and-construction.md
│       │   └── ...
│       ├── vol-02-cognition/
│       ├── vol-03-synthesis/
│       └── 9999-0-bm-0-acknowledgements.md ← backmatter
├── design/                                 ← design blueprints
└── assets/
    └── images/
```

---

## Quick Start

1. Clone the repo
2. Open `index.html` in a browser (or serve with any static server)
3. Reader loads `content/<book-slug>/toc.md` and renders chapters

```bash
# local dev server (optional)
npx serve .
```

No build step. No npm install. Just open the file.

---

## Add a New Chapter

1. Create `NNNN-0-ch-N-slug.md` in the appropriate volume folder
2. Add one line to `toc.md`
3. Done

---

## Add a New Book

1. Create `content/<new-book-slug>/` with `meta.md` and `toc.md`
2. Update `BOOK_PATH` in `index.html`

---

## Tech Stack

| What | How |
|---|---|
| Markup | HTML5 |
| Styling | Vanilla CSS (custom properties for theming) |
| Logic | Vanilla ES6+ JavaScript |
| Markdown | [marked.js](https://github.com/markedjs/marked) via CDN |
| Fonts | Google Fonts (Lora, Playfair Display, Inter) |
| Hosting | GitHub Pages (static) |

---

## License

MIT