# Contributing

Thanks for considering a contribution. This is a small, opinionated template — the bar is "does it keep the file count and mental overhead low?" If so, send it.

## Ground rules

- **No build step, no framework.** The whole app is one HTML file with inline CSS and vanilla JS. PRs that introduce bundlers, React, Tailwind compilers, or package-manifest lock files will be declined.
- **No hardcoded content.** Anything a user might reasonably want to change should live in `roadmap-data.json` / `roadmap-data.example.json`, not in `index.html`.
- **Browser support:** modern evergreen browsers (last 2 years of Chrome/Safari/Firefox/Edge). The `:has()` selector is load-bearing; no polyfills required.

## Reporting issues

Open a GitHub issue with:

1. What you were doing
2. What you expected to happen
3. What actually happened
4. Browser + version
5. A minimal `roadmap-data.json` that reproduces the problem, if relevant

Feature requests are welcome but please describe the use case, not the implementation.

## Development workflow

```bash
# 1. Fork and clone
git clone https://github.com/<your-user>/roadmap-template.git
cd roadmap-template

# 2. Create a branch
git checkout -b fix/short-description

# 3. Make your change, serve locally, verify
python3 -m http.server 8000
# open http://localhost:8000 — the page should render with
# roadmap-data.example.json by default.

# 4. Commit and push
git commit -m "fix: short description"
git push origin fix/short-description

# 5. Open a PR against main
```

### Commit style

Short, imperative, prefixed with a type:

```
feat: add status color for "blocked"
fix: month filter leaves empty Q divider visible
docs: clarify deployment on Cloudflare Pages
chore: tidy up .gitignore entries
refactor: extract renderHeader into its own function
```

Squash-merge is fine; the PR title becomes the commit message.

## Pull request checklist

- [ ] `roadmap-data.example.json` still parses (`node -e "JSON.parse(require('fs').readFileSync('roadmap-data.example.json'))"`).
- [ ] Page renders cleanly with and without a local `roadmap-data.json`.
- [ ] No new dependencies, build scripts, or `package.json`.
- [ ] No references to any private product or company in committed files.
- [ ] README / CLAUDE.md updated if behavior changed.

## Code style

- 2-space indentation in HTML, CSS, JS.
- Prefer named functions over arrow functions at the top level of the IIFE — it makes stack traces readable.
- Keep the JS IIFE pattern. Don't attach globals.
- Keep selectors specific. Avoid `!important`.

## What counts as "done"

A PR is ready to merge when the page renders for both the shipped example JSON and an empty / missing `roadmap-data.json`, on desktop and mobile widths, with no console errors.
