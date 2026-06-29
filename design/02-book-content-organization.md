# Book Content — Organization, Naming & Structure
### Addendum to the Reader Design Blueprint

---

## 1. Project Name Suggestions

The name should work as a GitHub repo slug, a URL path, and something you can say out loud without explaining it. Short, bookish, not already a major library.

| Name | Origin | Why it works |
|---|---|---|
| **Folio** | Latin for "leaf" — also a book format | Short, elegant, universally understood as bookish. Closest to perfect. |
| **Quire** | A gathering of folded pages in bookbinding | Obscure enough to be available, meaningful to anyone who knows it. |
| **Vellum** | Writing material made from calfskin, used for manuscripts | Evokes craft and permanence. Unusual. Sounds premium. |
| **Chapbook** | Historically a small self-published pamphlet | Directly apt — this is exactly what a chapbook is. Has a DIY warmth. |
| **Codex** | The format of a modern book (vs a scroll) | Strong word, slightly overused in dev projects. Check for conflicts. |
| **Lectern** | A reading stand, where a book rests open | Direct metaphor for a reader. Architectural, less expected. |

**Recommendation: `folio`**

`github.com/yourname/folio` reads perfectly. The URL `yourname.github.io/folio` is clean. The word means exactly what you are building — a leaf, a page, a book. It does not need explanation and it does not collide with any major package or tool in active use.

If folio is taken on GitHub by someone else under your preferred username, `quire` is the next best choice.

---

## 2. Folder Structure

### The principle

The reader is `index.html` — one file, lives at the root. Everything else serves the *content*. The structure should be so obvious that someone cloning the repo for the first time knows exactly where to put a new chapter without reading any documentation.

### Recommended structure

```
folio/
│
├── index.html                  ← the reader, standalone, do not touch once built
│
├── book.md                     ← book-level metadata (title, author, cover details)
│
├── content/
│   ├── toc.md                  ← table of contents, the reader parses this first
│   │
│   ├── 00-frontmatter.md       ← dedication, epigraph, opening quote
│   ├── 01-preface.md           ← or prologue — whichever applies
│   │
│   ├── ch-01-chapter-title.md  ← main chapters, prefixed with ch-
│   ├── ch-02-chapter-title.md
│   ├── ch-03-chapter-title.md
│   │   ... and so on
│   │
│   ├── 96-epilogue.md          ← or afterword
│   ├── 97-acknowledgements.md
│   └── 98-bibliography.md      ← if needed, omit if not
│
└── assets/
    └── images/
        ├── shared/             ← images referenced in multiple chapters or the cover
        ├── ch-01/              ← images for chapter 01 only
        ├── ch-02/
        └── ch-03/
```

### Why this exact naming

**`ch-` prefix for main chapters.** The prefix does two things: it keeps chapters sorted together in the filesystem (all `ch-` files cluster between frontmatter and back matter), and it gives the reader a signal about section type — the reader can style a `ch-` file differently from a `00-` or `96-` file without guessing.

**Two-digit numbers.** Files sort alphabetically. `ch-1` and `ch-10` do not sort the way you expect — `ch-10` comes before `ch-2`. Zero-padding to two digits (`ch-01`, `ch-10`) fixes this for up to 99 chapters. If you're writing something longer than that, go to three digits.

**Fixed numbers for front and back matter.** Frontmatter lives at `00`–`01`. Back matter lives at `96`–`98`. Main chapters live in the `ch-` namespace between them. This means you never run out of space and you never accidentally sort a preface after chapter 3.

**Images namespaced by chapter.** `assets/images/ch-01/` holds every image that appears in chapter 01. The chapter's MD file references them with a relative path: `../assets/images/ch-01/figure-01.jpg`. If an image is used in two chapters, put it in `shared/`.

### Alternative: chapter subfolders

If your chapters are heavy on images (more than 4–5 images per chapter), the flat structure becomes hard to navigate. In that case, give each chapter its own folder:

```
content/
├── toc.md
├── 00-frontmatter.md
├── ch-01-chapter-title/
│   ├── index.md               ← the chapter text
│   └── images/
│       ├── fig-01.jpg
│       └── fig-02.png
├── ch-02-chapter-title/
│   ├── index.md
│   └── images/
│       └── fig-01.jpg
└── 96-epilogue.md
```

Inside the chapter, images are referenced simply as `images/fig-01.jpg` — no `../assets/` needed. The chapter is self-contained. This is the right choice for a book where images are integral to the narrative rather than supplementary.

**Pick one and stick to it.** Mixing the two patterns in the same book creates inconsistent relative paths and will cause broken images.

---

## 3. The `toc.md` File — Single Source of Truth

The reader parses `toc.md` to build the sidebar, determine reading order, and display chapter metadata. Everything the reader needs to know about the book's structure comes from this one file. You never have to touch `index.html` to add a chapter — you add the MD file and update `toc.md`.

### Format

```markdown
---
title: The Architecture of Thought
author: Arindam
subtitle: An inquiry into perception, memory, and the stories we tell ourselves
cover: ../assets/images/shared/cover.jpg
language: en
year: 2025
---

## Front matter

- [Dedication & Epigraph](00-frontmatter.md)
- [Preface](01-preface.md)

## Chapters

- [The First Principles of Perception](ch-01-perception.md) | How the mind constructs the world it inhabits
- [Language as a Mirror](ch-02-language.md) | What we say reveals more than what we mean
- [The Weight of Memory](ch-03-memory.md) | Why the past is never where we left it
- [On Silence and Signal](ch-04-silence.md) | The information contained in what is not said

## Back matter

- [Epilogue](96-epilogue.md) | What remains
- [Acknowledgements](97-acknowledgements.md)
```

### Rules

- Each list item maps to exactly one MD file. The path is relative to `toc.md`.
- The text after `|` is a chapter subtitle shown in the sidebar. Optional — leave it out if you prefer clean ToC entries.
- Section headings (`## Chapters`) become non-clickable group labels in the sidebar.
- The YAML frontmatter at the top is used for the book's metadata (title page, EPUB generation, `<title>` tag in the browser).
- Adding a chapter = add one line to `toc.md` + create the MD file. That's the entire workflow.

---

## 4. Chapter MD Files — Format from Scratch

### Frontmatter

Every chapter starts with a YAML frontmatter block. This is not Markdown — it's metadata the reader strips out before rendering.

```yaml
---
title: The First Principles of Perception
chapter: 01
subtitle: How the mind constructs the world it inhabits
words: 3200
accent: coral
---
```

| Field | Purpose | Required |
|---|---|---|
| `title` | Displayed in running header and browser tab | Yes |
| `chapter` | Used for "Chapter 01" label in the chapter header | Yes |
| `subtitle` | Displayed under the chapter title in the opening | No |
| `words` | Reader uses this for reading time estimate | No (reader can count itself, but explicit is faster) |
| `accent` | Per-chapter accent color shift: `coral` / `amber` / `teal` / `default` | No |

### Chapter body structure

```markdown
---
(frontmatter here)
---

Opening paragraph here. This is the one the reader adds a drop cap to.
Do not put any heading before this — go straight into prose.

Continue with more paragraphs. Leave a blank line between each.

## Section heading (optional)

Use headings sparingly. If your chapter flows naturally without sections,
do not force headings. The reader styles h2 as a subtle section break.

### Subsection (use even more sparingly)

> This is a pull quote. Start blockquotes with a quotation mark
> and the reader styles them as a display pull quote.
> Anything else is styled as a standard indented quote.

This is regular prose continuing after the pull quote.

![Caption goes here as a figure caption](../assets/images/ch-01/fig-01.jpg)

---

A horizontal rule creates a section break — a small ornamental divider,
styled like a typographic dinkus rather than a plain line.
```

### What the reader does automatically

- First paragraph → drop cap on the first letter
- `> ` starting with a quotation mark → pull quote treatment
- `> ` without quotation mark → standard indented blockquote
- `![alt](path)` → lazy-loaded image with fade-in
- Any `*italic text*` immediately following an image (no blank line) → figure caption
- `---` → styled section break / dinkus
- `##` headings → section break with running header update

You do not add any special syntax or HTML for these — the reader handles it on parse.

---

## 5. Book Organization from Scratch — Writing Workflow

### Numbering strategy: leave gaps

Do not number chapters 01, 02, 03 sequentially from the start. Number them 01, 03, 05, or even 05, 10, 15 — in steps. Why: you will want to insert a chapter between two existing ones later. If chapters are 01 and 02, the new one has nowhere to go. If they are 01 and 05, you have room for 02, 03, 04.

Recommended: number main chapters by fives. `ch-05`, `ch-10`, `ch-15`. If you need to insert between 05 and 10, use `ch-06`, `ch-07`, etc. You will not run out of space with this approach.

### Drafts folder

Keep unfinished chapters out of the main content flow:

```
content/
├── toc.md
├── ch-05-chapter-one.md   ← published, in toc.md
├── ch-10-chapter-two.md   ← published, in toc.md
└── drafts/
    ├── ch-15-chapter-three.md   ← not in toc.md yet
    └── ch-20-idea-fragment.md   ← rough notes
```

`drafts/` is ignored by the reader because those files are not listed in `toc.md`. They travel with the repo, are backed up to GitHub, but are never shown to readers. When a chapter is ready, move it up and add it to `toc.md`.

### A clean starting scaffold

When you start a new book from scratch, create this skeleton:

```
folio/
├── index.html
├── book.md
├── content/
│   ├── toc.md
│   ├── 00-frontmatter.md
│   └── drafts/
│       └── ch-05-working-title.md
└── assets/
    └── images/
        └── shared/
```

Add `ch-` files as you write chapters. Add them to `toc.md` when they are ready to be read. Never delete or rename a file after you have published it — it will break anyone's saved reading position that pointed to that URL hash.

### File naming rules

- Lowercase only
- Hyphens instead of spaces or underscores
- Keep titles short in the filename — the full title lives in the frontmatter
- `ch-05-perception.md` is better than `ch-05-the-first-principles-of-human-visual-perception.md`
- Do not use special characters, apostrophes, or accented letters in filenames

---

## 6. EPUB Export — Feature Design

### What it is

A button in the reader (settings panel or top bar) that generates a valid `.epub` file in the browser and downloads it. No server, no backend, no API. Pure client-side.

### How it works internally

EPUB is a ZIP file with a specific internal structure. The browser can build a ZIP using JSZip, a ~95KB library that is loaded lazily — only when the user clicks "Download EPUB." The steps:

1. The reader already has all chapter content in memory (it fetched and parsed every visited chapter). For unvisited chapters, it fetches them at export time.
2. Each chapter's HTML is converted to valid XHTML (EPUB requires strict XML — no unclosed tags).
3. Images referenced in the book are fetched and base64-encoded into the EPUB's internal file structure.
4. The reader generates the required EPUB metadata files automatically from `book.md` and `toc.md`.
5. JSZip assembles everything into a ZIP, which the browser downloads as `your-book-title.epub`.

### EPUB internal structure (generated automatically)

```
your-book.epub  (ZIP)
├── mimetype                    ← must be first file, uncompressed
├── META-INF/
│   └── container.xml
└── OEBPS/
    ├── content.opf             ← manifest: lists every file in the book
    ├── toc.ncx                 ← old-style navigation (for compatibility)
    ├── toc.xhtml               ← EPUB3 navigation
    ├── styles.css              ← a clean readable stylesheet for e-readers
    ├── ch-01-perception.xhtml
    ├── ch-02-language.xhtml
    └── images/
        ├── ch-01/
        │   └── fig-01.jpg
        └── shared/
            └── cover.jpg
```

The reader generates `content.opf` and `toc.xhtml` dynamically from the same `toc.md` it uses for the sidebar — no duplication of effort.

### What the EPUB stylesheet includes

E-readers (Kindle, Kobo, Apple Books) strip most web CSS. The EPUB stylesheet is minimal and defensive:

- Lora is not embedded (too large for an EPUB). The stylesheet requests a generic serif and lets the e-reader use its own body font.
- Chapter title, chapter number, subtitle are styled.
- Drop cap is specified (many e-readers support it).
- Pull quotes get a left border and italic treatment.
- Images are constrained to 100% max-width.
- That is it. Over-engineering the EPUB CSS fights the e-reader and loses.

### User experience

- Button label: "Download as EPUB" — in the settings panel
- On click: a small spinner replaces the button text: "Building..." (this takes 1–4 seconds depending on how many images need fetching)
- On complete: file downloads automatically, button returns to normal
- If a chapter image fails to fetch (e.g. network is offline): that image is skipped and a note is added to the download (or the image space is left empty in the EPUB)

### Limitations to be aware of

- **Images require network access** at export time. If you are offline and have not visited all chapters, some images may be missing from the EPUB. This is a fundamental browser constraint.
- **Very large books** (100,000+ words with many images) may cause the browser to slow briefly during the ZIP assembly step. Nothing will break — it just takes a few more seconds.
- **DRM is not possible** client-side. The EPUB is unprotected. This is almost certainly what you want for a self-published book, but worth stating clearly.
- **EPUB validation**: the generated file will pass basic validators. If you want to submit to a distributor (Amazon KDP, Smashwords) it may need light manual cleanup of the OPF metadata.

### JSZip loading

JSZip is only loaded when the user clicks "Download EPUB" — it is not part of the reader's initial page load. This keeps the reader fast. The ~95KB downloads on demand, once, then the browser caches it for future exports.

---

## Summary of Decisions

| Decision | Recommendation |
|---|---|
| Project name | `folio` (or `quire` as fallback) |
| Chapter file prefix | `ch-XX-short-title.md` |
| Chapter numbering | Multiples of 5 (ch-05, ch-10, ch-15) to leave room for inserts |
| Front matter files | `00-`, `01-` prefix |
| Back matter files | `96-`, `97-`, `98-` prefix |
| Images | `assets/images/ch-XX/` for chapter images, `assets/images/shared/` for reused ones |
| Draft chapters | `content/drafts/` folder, not listed in `toc.md` |
| EPUB library | JSZip, loaded lazily on demand |
| EPUB images | Fetched and embedded at export time |
| TOC source of truth | `content/toc.md` — only file you edit when adding chapters |
