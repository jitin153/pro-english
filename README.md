# ProEnglish

A clean, offline-first web app to learn English from basic to proficient — vocabulary, daily phrases, idioms, slang, phrasal verbs, proverbs, common mistakes, and confusing words, each with Hindi meanings and examples. It runs as a **pure static site**: just open the HTML file in a browser, no server or build step required.

## Features

* Eight learning sections in one place: Vocabulary, Daily Phrases, Idioms, Slang, Phrasal Verbs, Proverbs, Common Mistakes, and Confusing Words.
* Hindi meanings and example sentences for every entry, with a practice mode that hides the Hindi until you reveal it.
* Search, filter (by level, part of speech, category), and sort within each section.
* Progress tracking — mark words and phrases as learned/mastered; your progress is saved in the browser.
* Light and dark themes, responsive layout, and PDF export.
* **Data-driven**: all content lives in plain data files, so you can grow the app just by adding files — no code changes.
* **Fault-tolerant loading**: a bad file or a malformed entry is skipped (never crashes the app), and a "Data load report" shows exactly what loaded and what was skipped.

## Quick start

1. Download and unzip the project.
2. Double-click `proenglish.html` to open it in your browser. That's it — the data loads automatically.

The app loads its content with `<script src>` tags (which browsers allow on `file://`), so it works with no web server. If you prefer to serve it, any static server works too:

```
python -m http.server 8000
```

Then open `http://localhost:8000/proenglish.html`.

## Project structure

```
proenglish.html          The complete app (HTML + CSS + JS, self-contained)
data/                    All learning content
  vocabulary-1.js
  daily-phrases-1.js
  idioms-1.js
  slang-1.js
  phrasal-verbs-1.js
  proverbs-1.js
  common-mistakes-1.js
  confusing-words-1.js
  README.txt             Notes about the data format
README.md                This file
```

## How the data works

Each data file is a small JavaScript file that registers its own content by calling a global function:

```js
AS_DATA("vocabulary", [
  { "word": "Happy", "hindi": "खुश / प्रसन्न", "pos": "adjective", "level": "Basic", "category": "Emotions",
    "examples": ["I am very happy today.", "She looked happy after hearing the news."] }
]);
```

The first argument is the **section prefix**; the second is an **array of entries**. The app finds files by their filename prefix and merges everything into the matching section.

### Filename → section map

| Section | Filename prefix | Examples |
|---|---|---|
| Vocabulary | `vocabulary` | `vocabulary-1.js`, `vocabulary-2.js` |
| Daily Phrases | `daily-phrases` | `daily-phrases-1.js` |
| Idioms | `idioms` | `idioms-1.js` |
| Slang | `slang` | `slang-1.js` |
| Phrasal Verbs | `phrasal-verbs` | `phrasal-verbs-1.js` |
| Proverbs | `proverbs` | `proverbs-1.js` |
| Common Mistakes | `common-mistakes` | `common-mistakes-1.js` |
| Confusing Words | `confusing-words` | `confusing-words-1.js` |

## Adding more content

To add data, just drop a new `.js` file into the `data/` folder whose name **starts with the section prefix** and ends with a number, for example `vocabulary-2.js`:

```js
AS_DATA("vocabulary", [
  { "word": "Eloquent", "hindi": "प्रभावशाली वक्ता", "pos": "adjective", "level": "Advanced",
    "category": "Communication", "examples": ["She gave an eloquent speech."] }
]);
```

Reload the page and the new entries appear automatically. There is **no manifest to edit and no HTML to change** — numbered files (`-1`, `-2`, `-3`, …) are discovered in sequence.

### Entry formats

Vocabulary entry:

```json
{ "word": "…", "hindi": "…", "pos": "…", "level": "Basic|Intermediate|Advanced",
  "category": "…", "examples": ["…", "…"] }
```

Daily phrase entry:

```json
{ "english": "…", "hindi": "…", "category": "…" }
```

Idioms / Slang / Phrasal Verbs / Proverbs / Common Mistakes / Confusing Words use a term/meaning/examples shape and are flexible about exact field names.

The loader is forgiving: common alternate field names are mapped automatically (for example `meaning` or `translation` → `hindi`, `example` → `examples`), and missing optional fields fall back to sensible defaults.

## Fault tolerance and the Data load report

The app never lets one bad file or entry break the experience:

* If a data **file** fails to load or has a script error, it is skipped and the app keeps loading the rest.
* If a single **entry** is malformed (not an object, or missing a required field like `word` or `english`), that entry is skipped and the rest of the file still loads.
* Every skip is logged to the browser **console** with the file name, entry number, and reason.
* A **Data load report** (open it from the clipboard icon in the top bar) summarizes how many entries loaded, how many were skipped, and why — per file. It opens automatically the first time if anything was skipped.

The result: only correct entries from correct files are shown, and the app stays fully usable no matter what.

## Tech

Built as a single self-contained HTML file using [Bootstrap 5.3](https://getbootstrap.com/) and [Bootstrap Icons](https://icons.getbootstrap.com/) (both MIT licensed). No build tools, no backend, no external data calls.
