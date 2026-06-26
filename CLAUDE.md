# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A personal digital garden published at **notes.kdayno.com**, built on the **Quartz v5**
framework. The Quartz framework is **vendored** (the repo was cloned from
`github.com/jackyzha0/quartz`, not installed as a dependency), so the framework source
lives alongside the user's content in the same tree. The vast majority of files are
upstream Quartz; the user-owned surface is small (see "What you'll actually edit").

## Commands

```bash
npx quartz build --serve     # build + watch + serve at http://localhost:8080 (dev)
npx quartz build             # one-off production build into public/
npx quartz create            # (re)initialize content/config — already done
npx quartz update            # pull framework updates from the upstream remote
npx prettier . --write       # format (also: npm run format)
npm run check                # tsc --noEmit + prettier --check
npm test                     # tsx --test (framework tests; rarely needed for content work)
```

Requires Node ≥22 (`.npmrc` sets `engine-strict=true`; `.node-version` pins v22.16.0).

> [!important] Clean rebuild after layout/config/style changes
> The `--serve` watcher does **not** always fully recompute layout after edits to
> `quartz.config.yaml` or `quartz/styles/*.scss` — it can serve stale output (e.g. a
> component rendered in the wrong sidebar). When changing config or styles, do a clean
> rebuild and restart:
> ```bash
> pkill -f "quartz build --serve"; rm -rf public .quartz-cache; npx quartz build && npx quartz build --serve
> ```
> Editing files under `content/` hot-reloads fine and does not need this.

## What you'll actually edit (user-owned surface)

Everything else is upstream framework code — avoid editing it unless intentionally
patching Quartz.

- **`content/`** — all notes, as Markdown with YAML frontmatter. `content/index.md` is the
  homepage. Subfolders (`notes/`, `thoughts/`) auto-generate folder pages and Explorer
  entries. Links between notes use Obsidian-style wikilinks: `[[slug]]` or `[[slug|text]]`.
- **`quartz.config.yaml`** — site config: `pageTitle`, `baseUrl`, theme (colors/fonts), the
  `plugins:` list, and the `layout:` section. This is the v5 YAML config — **not** the v4
  `quartz.config.ts`. `quartz.config.default.yaml` is the upstream template; don't edit it.
- **`quartz/styles/custom.scss`** — the one intended place for custom CSS (per Quartz docs).
- **`vercel.json`** — deploy config (`cleanUrls`).

## Architecture notes that aren't obvious from one file

- **Plugins are a remote registry, not local code.** The `plugins:` list in
  `quartz.config.yaml` references `github:quartz-community/*` repos. They are fetched at
  build time (the `prebuild`/`install-plugins` npm hook), pinned in `quartz.lock.json`, and
  cached under `.quartz/plugins/<name>/` (each has its own `src/` + bundled styles). To learn
  a component's options or default styling, read its source there — e.g.
  `.quartz/plugins/search/src/components/Search.tsx` and `.../styles/search.scss`.

- **Layout = positions + groups.** Each plugin's `layout:` block sets `position`
  (`left`/`right`/`beforeBody`/...) and `priority` (order within a position). Plugins can
  share a horizontal `group` (e.g. `toolbar`), and `groupOptions: { grow: true }` makes a
  flex item expand. The header on this site is a `toolbar` group containing `page-title`, a
  growing `spacer` (the gap that pushes icons right), `search`, and `darkmode`.

- **Custom-CSS cascade gotcha.** `custom.scss` is bundled into a stylesheet that loads
  *before* the per-component stylesheets. So an equal-specificity override of a component
  rule (e.g. `.search > .search-button`) **loses** the cascade. Layout-renderer styles like
  `flex-grow` are applied as **inline** styles, which external CSS also can't beat normally.
  For both cases, override with `!important` (see existing rules in `custom.scss` for the
  icon-only search button, resized title, and resized toolbar icons).

## Intentional framework patches

These deviate from upstream and may conflict on `npx quartz update` — re-apply if so:

- **`quartz/components/Head.tsx`** — injects a small loader for **Pocket-Bird**
  (`github.com/IdreesInc/Pocket-Bird`, MPL-2.0), a pixel-art bird pet. The embed is
  self-hosted at `quartz/static/birb.embed.js` (served at `/static/birb.embed.js`). The
  embed appends `#birb-shadow-host` to `<body>` once and is not SPA-aware, so the loader
  re-attaches it on the `"nav"` event (fired on load + every SPA navigation) with a
  duplicate guard.
- **`quartz/static/birb.embed.js`** — vendored Pocket-Bird embed with one local edit: the
  `DEFAULT_BIRD` constant is changed from `"bluebird"` to `"redWarbler"` (the default species
  for new visitors). The chosen bird is stored per-browser in `localStorage["birbSaveData"]`,
  so changing the default only affects browsers with no saved data yet.

## Git & deploy

- Branch `main`. Remote `upstream` → Quartz (for `npx quartz update`); the user's own
  `origin` (GitHub repo `notes.kdayno.com`) is added separately when publishing.
- `.gitignore` ignores `public/`, `node_modules`, `.quartz-cache`, and (notably) `.gitignore`
  itself.
- Deploy target: **Vercel** — Framework `Other`, Output `public`, Install `npm install`, and
  Build Command **`npx quartz plugin install && npx quartz build`** (NOT just `npx quartz build`).
  `.quartz/` (installed plugins) is gitignored, so they're absent on Vercel's fresh clone and must
  be installed during the build, else the build stalls right after the `Quartz vX.Y.Z` banner.
