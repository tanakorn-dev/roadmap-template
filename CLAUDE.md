# CLAUDE.md

Guidance for AI assistants (Claude, GitHub Copilot, Cursor, etc.) working in this repository.

## What this project is

A **static, single-file product roadmap template**. One `index.html` with inline CSS and vanilla JS renders a timeline view of features grouped by quarter and month. All content comes from `roadmap-data.json` (or `roadmap-data.example.json` as fallback).

The repo is an open source template — users fork it, drop in their own JSON, and publish.

## Architecture

```
index.html                       # HTML + CSS + vanilla JS renderer (single file)
roadmap-data.example.json        # Demo data shipped with the repo
roadmap-data.json                # User's real data — gitignored, takes precedence
```

There is **no build step**. No `package.json`, no bundler, no framework. The browser loads `index.html`, runs the inline JS, fetches `roadmap-data.json` (falling back to the example file), and renders.

### Render pipeline

On `DOMContentLoaded` the IIFE in `<script>`:

1. `loadData()` — tries `roadmap-data.json`, falls back to `roadmap-data.example.json`, otherwise calls `renderLoadError(err)`.
2. `renderHeader(meta)` — title, eyebrow, owner, updated (formatted via `formatDate()` from ISO `YYYY-MM-DD` to `Mon D, YYYY`), version. Updates `document.title`.
3. `renderFilter(data)` — generates the "All | Jan | Feb | …" pill buttons. The button pre-marked `active` is whichever month has `current: true`, or `All` when none does (`defaultFilterId(data)` decides).
4. `renderSummary(data)` — counts cards by status.
5. `renderTimeline(data)` — Q dividers + month rows + cards.
6. `renderFooter(meta)` — footer text.
7. `initFilter(data)` — wires click handlers and immediately calls `applyFilter()` against the initially-active button so the visible rows + Q dividers match the picker's default selection on load.

### Key CSS behaviors

- Status color is set once on `--done`, `--in-progress`, `--planned`, `--research` custom properties and reused via `.badge-*`, `.stripe-*`, `.summary-num.*`, and `.card-feature:has(.stripe-*)`.
- `.card.card-feature:has(.stripe-<status>) { border-left-color: ... }` requires the CSS `:has()` selector. Don't replace it without a plan for older browsers.
- `@keyframes fadeUp` staggers month rows on load via `:nth-child()` delays.

## Common tasks

### Adding a new status (e.g., `blocked`)

1. Add `--blocked` and `--blocked-light` to `:root` in `index.html`.
2. Add a `.badge-blocked`, `.stripe-blocked`, `.summary-num.blocked` rule.
3. Add a `:has(.stripe-blocked)` rule under `.card.card-feature`.
4. Extend `STATUS_MAP` and `SUMMARY_MAP` in the `<script>` block.
5. Add a `badge` entry to the `.legend-bar` in the HTML.
6. Document the new status in `README.md` under **JSON Schema**.

### Changing colors, fonts, layout

Colors: edit CSS custom properties at the top of `<style>`. Fonts: edit the Google Fonts `<link>` plus `font-family` declarations. Don't introduce CSS files.

### Adding a new meta field to the header

1. Add the key to `meta` in `roadmap-data.example.json`.
2. Extend the `items.push({...})` list in `renderHeader()`.
3. Document the field in the README's **JSON Schema** section.

## Constraints — please respect these

- **No build step.** Don't add `package.json`, webpack, Vite, Tailwind compilation, TypeScript, etc.
- **No frameworks.** No React, Vue, Svelte, Alpine, htmx.
- **Single HTML file** — keep CSS and JS inline. The whole point is "one file to read, one file to deploy."
- **No external JS runtime dependencies.** The only network request is Google Fonts and the roadmap JSON.
- **Escape user-supplied strings.** `renderCard()`, `renderMonth()`, etc. pass JSON values through `escapeHtml()` before inserting into the DOM. `meta.title` allows a single `<em>` tag; everything else must stay escaped.
- **No private / company-specific content.** Committed files must be generic. Example data should reference fake products only.

## Data privacy

`roadmap-data.json` is in `.gitignore` on purpose — users shouldn't commit their real roadmap if they fork the template publicly. When adding features, don't introduce behavior that writes user data back to committed files.

## Testing

There are no automated browser tests. Before merging, verify:

```bash
# JSON is still valid
node -e "JSON.parse(require('fs').readFileSync('roadmap-data.example.json','utf8'))"

# Page renders with example data
python3 -m http.server 8000
# Open http://localhost:8000 and scroll through desktop + mobile widths.
```

CI (`.github/workflows/validate.yml`) runs JSON validation on PR.

## When in doubt

Keep the repo small. The goal is that a non-developer can fork this, edit one JSON file, and publish. Every addition should be justifiable against that goal.
