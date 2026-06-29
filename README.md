# 📰 Alma Communiqué

![Frontend](https://img.shields.io/badge/Frontend-HTML5%20%7C%20CSS3%20%7C%20JavaScript-E34F26?logo=html5)
![Built with](https://img.shields.io/badge/Vanilla%20JS-ES6+-F7DF1E?logo=javascript&logoColor=black)
![Framework](https://img.shields.io/badge/Tailwind%20CSS-3.x-06B6D4?logo=tailwindcss)
![Data](https://img.shields.io/badge/Data-Google%20Sheets-34A853?logo=googlesheets)
![Organization](https://img.shields.io/badge/IIT(BHU)-SAIC-800000)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

> *Connecting the IIT (BHU) alumni fraternity — one edition at a time.*

**Alma Communiqué** is built as the digital newsletter platform of the Student Alumni Interaction Cell (SAIC), IIT (BHU) Varanasi. It is a fully dynamic, zero-backend web application that pulls live article data from Google Sheets and renders a polished, magazine-style reading experience — with dark mode, live search, multi-edition archives, and a creative showcase, all in a single HTML file with no build step.

---

## 📋 Table of Contents

- [Features](#-features)
- [Architecture](#️-architecture)
- [Data Pipeline](#-data-pipeline)
- [How to Add a New Edition](#-how-to-add-a-new-edition)
- [Configuration Reference](#-configuration-reference)
- [Project Structure](#-project-structure)

---

## ✨ Features

<!-- DESIGN NOTE: Add a GIF here showing the app opening, switching editions, using search, and opening the article modal. Record with Loom or Chrome's built-in recorder, export as GIF. Save as: ![App Demo](assets/demo.gif) -->

### Reading Experience
- ✅ Magazine-style article feed with full-bleed cover images
- ✅ One-click article modal with full content, category badge, and date
- ✅ "More News" sidebar inside the modal for seamless browsing
- ✅ Live reading progress bar at the top of the viewport
- ✅ "Go to Top" floating button with smooth scroll

### Content & Data
- ✅ **Zero-backend architecture** — all data pulled live from published Google Sheets CSVs
- ✅ Multi-edition support with dropdown switcher (January, February, March 2026+)
- ✅ Dynamic patent/project tables fetched per edition from separate Sheets
- ✅ Creative showcase modal with per-edition artwork

### Navigation & Search
- ✅ Multi-select category filter chips with active state
- ✅ Full-text fuzzy search across all article fields (headline, summary, category, date)
- ✅ Animated centered search overlay with live results dropdown
- ✅ Edition archive dropdown with active edition highlight

### UI & Accessibility
- ✅ Full dark mode with `localStorage` persistence
- ✅ Fully responsive — mobile-first layout with iOS scroll fixes
- ✅ Smart footer collision detection (floating buttons shift up when footer appears)
- ✅ Smooth modal transitions with backdrop blur
- ✅ `onerror` image fallbacks for all article images

---

## 🏗️ Architecture

Alma Communiqué is a **single-file, zero-dependency\* web app** with a clear internal separation of concerns:

```
index.html
├── DATA LAYER          — EDITION_SOURCES, EDITION_TABLES, EDITION_CREATIVES (config objects)
├── STATE MANAGEMENT    — state{} object (activeEdition, articles, categories, searchQuery, isDarkMode)
├── DATA FETCHING       — fetchGoogleSheetData(), fetchTablesForEdition(), parseCSV(), parseTableCSV()
├── RENDER ENGINE       — renderArticles(), renderCategories(), renderTables(), renderEditionMenu()
├── FILTER ENGINE       — getFilteredArticles() (category + multi-term fuzzy search)
├── MODAL SYSTEM        — openModal(), closeModal(), switchModalArticle()
└── EVENT LAYER         — setupEventListeners() (scroll, search, theme, edition, modals)
```

\*Runtime dependencies loaded via CDN: Tailwind CSS, Lucide Icons, Google Fonts (Montserrat).

### State Flow

```
User Action
    ↓
state{} mutation (edition / category / searchQuery)
    ↓
getFilteredArticles() — applies category + search filters
    ↓
renderArticles() + renderTables() + renderCategories()
    ↓
DOM update (no framework, direct innerHTML injection)
```

---

## 📊 Data Pipeline

All content is managed entirely through **Google Sheets** — no CMS, no database, no backend.

### Google Sheet → App Flow

```
Google Sheet (Editor view)
    → File > Share > Publish to web
    → Format: CSV
    → Published URL added to EDITION_SOURCES{}
        ↓
fetch() on page load / edition switch
        ↓
parseCSV() maps headers → article objects
        ↓
renderArticles() injects HTML into DOM
```

### Required Sheet Column Headers

The CSV parser auto-detects column intent using keyword matching:

| Sheet Column Name | Maps To | Notes |
|---|---|---|
| `Title` / `Headline` | `article.Headline` | Article title |
| `Short Info` / `Summary` | `article.Summary` | Card preview text |
| `Entire Content` / `Full Content` | `article.Full_Content` | Modal body |
| `Image Link` / `Image` | `article.Image_URL` | Cover photo URL |
| `Link to Article` / `Link` | `article.External_Link` | Optional external URL |
| `Category` / `Section` | `article.Category` | Used for filter chips |
| `Date` | `article.Date` | Displayed on card and modal |

Headers are **case-insensitive** and matched by keyword — exact naming is not required.

---

## ➕ How to Add a New Edition

Adding a new monthly edition requires **4 lines of code**:

**Step 1 — Publish your Google Sheet as CSV** (File → Share → Publish to web → CSV)

**Step 2 — Add the edition to all four config objects in `index.html`:**

```javascript
// 1. Articles source
const EDITION_SOURCES = {
    "April 2026": "https://docs.google.com/spreadsheets/d/e/YOUR_NEW_CSV_URL/pub?output=csv",
    "March 2026": "...",
    // ...
};

// 2. Tables (patents, projects)
const EDITION_TABLES = {
    "April 2026": [
        { title: "Patents Granted in April", url: "YOUR_TABLE_CSV_URL" },
        { title: "Patents Filed in April",   url: "YOUR_TABLE_CSV_URL" },
        { title: "Projects sanctioned in April", url: "YOUR_TABLE_CSV_URL" }
    ],
    // ...
};

// 3. Creative artwork image
const EDITION_CREATIVES = {
    "April 2026": "https://your-image-host.com/april-creative.jpg",
    // ...
};

// 4. Edition dropdown list
editionsList: ["April 2026", "March 2026", "February 2026", "January 2026"]
```

**Step 3 — Push to GitHub.** The new edition appears instantly in the dropdown.

---

## ⚙️ Configuration Reference

| Config Object | Purpose |
|---|---|
| `EDITION_SOURCES` | Maps edition names → Google Sheets CSV URLs for articles |
| `EDITION_TABLES` | Maps edition names → array of `{title, url}` for data tables |
| `EDITION_CREATIVES` | Maps edition names → image URL for the Creative modal |
| `state.editionsList` | Controls the order of editions in the dropdown |
| `tailwind.config` | Brand colors (`brand-purple`, `brand-gold`, etc.) |

### Brand Colors

| Token | Hex | Usage |
|---|---|---|
| `brand-purple` | `#6F265D` | Primary brand color |
| `brand-darkPurple` | `#4A193E` | Nav bar, header |
| `brand-gold` | `#F4DEC4` | Accents, badges, highlights |
| `brand-red` | `#B83A42` | Alerts |
| `brand-bubbleBg` | `#701462` | Active category chips |

---

## 
> All application logic, styles, and markup live in `index.html`. External resources (Tailwind, Lucide, fonts) are loaded from CDN at runtime.

---
<p>
<"center">
 Zero backend · Pure web
</p>
