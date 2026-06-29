<p align="center">
  <img src="https://em-content.zobj.net/source/apple/391/open-book_1f4d6.png" width="100" />
</p>

<h1 align="center">folio</h1>

<p align="center">
  <strong>A blazing-fast, magazine-styled static book reader for Markdown content</strong>
</p>

<p align="center">
  <em>Write in Markdown. Read like a magazine. Host anywhere.</em>
</p>

<p align="center">
  <a href="https://github.com/SinghArindam/folio/stargazers"><img src="https://img.shields.io/github/stars/SinghArindam/folio?style=for-the-badge&logo=github&color=C4572A&logoColor=white" alt="Stars"></a>
  <a href="https://github.com/SinghArindam/folio/network/members"><img src="https://img.shields.io/github/forks/SinghArindam/folio?style=for-the-badge&logo=git&color=D49B18&logoColor=white" alt="Forks"></a>
  <a href="https://github.com/SinghArindam/folio/issues"><img src="https://img.shields.io/github/issues/SinghArindam/folio?style=for-the-badge&logo=github&color=E07A4A&logoColor=white" alt="Issues"></a>
  <a href="LICENSE"><img src="https://img.shields.io/github/license/SinghArindam/folio?style=for-the-badge&color=5A8A5A&logoColor=white" alt="License"></a>
</p>

<p align="center">
  <a href="https://github.com/SinghArindam/folio"><img src="https://img.shields.io/badge/Framework-None_(Vanilla)-1E160A?style=flat-square&logo=javascript&logoColor=EFB93A" alt="Vanilla JS"></a>
  <a href="https://github.com/SinghArindam/folio"><img src="https://img.shields.io/badge/Size-~130KB_total-C4572A?style=flat-square&logo=webpack&logoColor=white" alt="Size"></a>
  <a href="https://github.com/SinghArindam/folio"><img src="https://img.shields.io/badge/Build-Zero_Steps-5A8A5A?style=flat-square&logo=checkmarx&logoColor=white" alt="Zero Build"></a>
  <a href="https://github.com/SinghArindam/folio"><img src="https://img.shields.io/badge/Hosting-GitHub_Pages-1F160C?style=flat-square&logo=github-pages&logoColor=white" alt="GitHub Pages"></a>
  <a href="https://github.com/SinghArindam/folio/commits/main"><img src="https://img.shields.io/github/last-commit/SinghArindam/folio?style=flat-square&color=6B5438" alt="Last Commit"></a>
  <a href="https://github.com/SinghArindam/folio"><img src="https://img.shields.io/github/repo-size/SinghArindam/folio?style=flat-square&color=AA9070" alt="Repo Size"></a>
  <img src="https://komarev.com/ghpvc/?username=SinghArindam&label=Profile%20Views&color=C4572A&style=flat-square" alt="Profile Views">
</p>

<br>

<p align="center">
  <a href="#-features">Features</a> •
  <a href="#%EF%B8%8F-themes">Themes</a> •
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-project-structure">Structure</a> •
  <a href="#-file-naming">Naming</a> •
  <a href="#-add-content">Add Content</a> •
  <a href="#-tech-stack">Tech Stack</a>
</p>

---

<br>

## ✨ Features

<table>
<tr>
<td width="50%">

### 📖 Reading Experience
- **Magazine-styled typography** — Lora serif body, Playfair Display chapter numbers, Inter UI
- **Drop caps** — Automatic decorative first-letter on every chapter opening
- **Pull quotes** — Blockquotes styled as editorial callouts
- **Section dinkus** — Elegant typographic breaks (`⁂`) instead of plain `<hr>`
- **Reading time estimates** — Per-chapter word count → minutes remaining

</td>
<td width="50%">

### 🎨 Three Themes
- **☀ Light** — Warm parchment off-white, never harsh
- **☾ Dark** — Deep brown-black like candlelit room
- **✦ AMOLED** — True `#000000` black for OLED displays with glowing progress bar

All transitions 200ms. Persisted in `localStorage`. No flash on reload.

</td>
</tr>
<tr>
<td>

### 📚 Volume & Arc Support
- **Flat books** — Simple `ch-01.md`, `ch-02.md` in one directory
- **Volumes** — `vol-01-awakening/` subfolders with chapters inside
- **Arcs** — `arc-01-prelude/` for web novels and sagas
- **Collapsible sidebar** — Accordion headers expand/collapse volume groups
- **Mix freely** — Frontmatter and backmatter sit alongside volumes

</td>
<td>

### ⌨️ Navigation
- **Keyboard** — `←/→` scroll pages, `N/P` chapters, `T` sidebar, `Esc` close
- **Touch/swipe** — Swipe from left edge opens sidebar on mobile
- **Deep linking** — Hash URLs for every chapter (`#vol-01/ch-01.md`)
- **Progress bar** — 2px coral bar tracks position across entire book
- **Font sizing** — 8 steps from 15px to 24px body text

</td>
</tr>
</table>

<br>

## 🖌️ Themes

<table>
<tr>
<td align="center" width="33%">

### ☀ Light
`#FAF7F2` warm parchment<br>
`#C4572A` coral accent<br>
<br>
<img src="https://img.shields.io/badge/-%23FAF7F2-FAF7F2?style=for-the-badge" alt="bg"><img src="https://img.shields.io/badge/-%23C4572A-C4572A?style=for-the-badge" alt="accent"><img src="https://img.shields.io/badge/-%231E160A-1E160A?style=for-the-badge" alt="text">

</td>
<td align="center" width="33%">

### ☾ Dark
`#17110A` warm brown-black<br>
`#E07A4A` bright coral<br>
<br>
<img src="https://img.shields.io/badge/-%2317110A-17110A?style=for-the-badge" alt="bg"><img src="https://img.shields.io/badge/-%23E07A4A-E07A4A?style=for-the-badge" alt="accent"><img src="https://img.shields.io/badge/-%23EAE0D2-EAE0D2?style=for-the-badge" alt="text">

</td>
<td align="center" width="33%">

### ✦ AMOLED
`#000000` absolute black<br>
`#F08040` vivid coral<br>
<br>
<img src="https://img.shields.io/badge/-%23000000-000000?style=for-the-badge" alt="bg"><img src="https://img.shields.io/badge/-%23F08040-F08040?style=for-the-badge" alt="accent"><img src="https://img.shields.io/badge/-%23F2E8D8-F2E8D8?style=for-the-badge" alt="text">

</td>
</tr>
</table>

<br>

## 🚀 Quick Start

```bash
# clone
git clone https://github.com/SinghArindam/folio.git
cd folio

# open directly (no build step needed)
open index.html        # macOS
start index.html       # Windows
xdg-open index.html    # Linux

# or use any static server
npx serve .
```

> **Zero build. Zero npm install. Zero config.** Just open the file.

<br>

## 📁 Project Structure

```
folio/
│
├── index.html                              ← standalone reader (all CSS + JS inlined)
│
├── content/
│   └── the-architecture-of-thought/        ← book folder (slug)
│       ├── meta.md                         ← book metadata (title, author, cover)
│       ├── toc.md                          ← table of contents — source of truth
│       │
│       ├── 0000-0-fm-0-dedication.md       ← frontmatter
│       ├── 0000-1-fm-0-preface.md
│       │
│       ├── vol-01-perception/              ← volume 1 (12 chapters)
│       │   ├── 0001-0-ch-1-perception-and-construction.md
│       │   ├── 0002-0-ch-2-language-as-a-mirror.md
│       │   └── ...
│       │
│       ├── vol-02-cognition/               ← volume 2 (12 chapters)
│       ├── vol-03-synthesis/               ← volume 3 (12 chapters)
│       │
│       └── 9999-0-bm-0-acknowledgements.md ← backmatter
│
├── design/                                 ← design blueprints & specs
│   ├── 01-book-reader-design-blueprint.md
│   ├── 02-book-content-organization.md
│   └── main-design.md                     ← combined main blueprint
│
└── assets/
    └── images/
        └── shared/
```

<br>

## 🏷️ File Naming Convention

Every content file follows a strict tokenized format:

```
[SEQ:4]-[PART]-[TYPE]-[NUM]-[SLUG].md
```

| Token | Format | Description | Example |
|:---:|:---:|---|:---:|
| **SEQ** | `0000`–`9999` | 4-digit zero-padded global sort order | `0001` |
| **PART** | `0`, `1`, `2`… | Multi-file chapter split index | `0` |
| **TYPE** | `fm` `ch` `bm` | Frontmatter / Chapter / Backmatter | `ch` |
| **NUM** | Integer | Explicit chapter or section number | `1` |
| **SLUG** | kebab-case | Human-readable title | `perception` |

<details>
<summary><strong>📋 Examples</strong></summary>

| File | What |
|---|---|
| `0000-0-fm-0-dedication.md` | Frontmatter: dedication page |
| `0000-1-fm-0-preface.md` | Frontmatter: preface (part 1) |
| `0001-0-ch-1-perception.md` | Chapter 1, single file |
| `0015-0-ch-15-narrative-self.md` | Chapter 15 |
| `0015-1-ch-15-narrative-self.md` | Chapter 15, continued (part 1) |
| `9999-0-bm-0-acknowledgements.md` | Backmatter: acknowledgements |

</details>

<br>

## ➕ Add Content

### Add a chapter

1. Create `NNNN-0-ch-N-slug.md` in the target volume folder
2. Add one line to `toc.md`:
   ```markdown
   - [Chapter Title](vol-01-perception/NNNN-0-ch-N-slug.md) | Optional subtitle
   ```
3. Done. No build. No config. Refresh browser.

### Add a volume

1. Create `vol-NN-name/` folder inside your book directory
2. Add chapters inside it
3. Add a `## Volume N: Title` heading in `toc.md` above the chapter links

### Add a new book

1. Create `content/<new-book-slug>/` with `meta.md` and `toc.md`
2. Update `BOOK_PATH` constant in `index.html`

<br>

## 🔧 Tech Stack

<table>
<tr>
<td align="center" width="120">
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/html5/html5-original.svg" width="40" /><br>
<strong>HTML5</strong><br>
<sub>Structure</sub>
</td>
<td align="center" width="120">
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/css3/css3-original.svg" width="40" /><br>
<strong>CSS3</strong><br>
<sub>Custom Properties</sub>
</td>
<td align="center" width="120">
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/javascript/javascript-original.svg" width="40" /><br>
<strong>ES6+</strong><br>
<sub>Zero frameworks</sub>
</td>
<td align="center" width="120">
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/markdown/markdown-original.svg" width="40" /><br>
<strong>Markdown</strong><br>
<sub>Content format</sub>
</td>
<td align="center" width="120">
<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/google/google-original.svg" width="40" /><br>
<strong>Google Fonts</strong><br>
<sub>Lora · Playfair · Inter</sub>
</td>
</tr>
</table>

<br>

<table>
<tr>
<td>

### Performance Budget

```
┌──────────────────────────────────────────┐
│  index.html (inlined CSS + JS)   ~15 KB  │
│  reader styles                   ~20 KB  │
│  reader logic                    ~30 KB  │
│  marked.js (CDN, cached)        ~25 KB  │
│  fonts (subset, cached)         ~40 KB  │
│─────────────────────────────────────────│
│  TOTAL FIRST LOAD               ~130 KB  │
│  WARM CACHE RELOAD               ~15 KB  │
│  FIRST PAINT TARGET             <500 ms  │
└──────────────────────────────────────────┘
```

</td>
<td>

### Keyboard Shortcuts

| Key | Action |
|:---:|---|
| `→` `L` | Scroll forward |
| `←` `H` | Scroll backward |
| `N` | Next chapter |
| `P` | Previous chapter |
| `T` | Toggle sidebar |
| `Esc` | Close sidebar |

</td>
</tr>
</table>

<br>

## 🗺️ Roadmap

- [ ] EPUB export (client-side via JSZip)
- [ ] Bookmark system with localStorage
- [ ] Text highlighting & marginalia
- [ ] Per-chapter ambient accent colors
- [ ] Full-text search across chapters
- [ ] PWA / offline support
- [ ] Print stylesheet
- [ ] Multi-book index page

<br>

## 📄 License

[MIT](LICENSE) — use it, fork it, make it yours.

<br>

---

<p align="center">
  <sub>Built with obsessive attention to typography and zero dependencies.</sub><br>
  <sub>Why use many kilobytes when few do trick.</sub>
</p>

<p align="center">
  <a href="https://github.com/SinghArindam/folio"><img src="https://img.shields.io/badge/⭐_Star_this_repo-C4572A?style=for-the-badge&logoColor=white" alt="Star"></a>
</p>