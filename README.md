# xr-app-data

Single source of truth for [xr-lab-library](https://github.com/Lone-Star-XR-Lab/xr-lab-library) and [xr-app-catalogue](https://github.com/Lone-Star-XR-Lab/xr-lab-catalogue). Both sites fetch data and images from this repo at runtime — nothing is copied between repos.

## Contents

- `data/apps.json` — every XR app, merged from both sites' former local copies. See `data/schema.json` for field definitions.
- `data/videos.json` — curated YouTube/360 video collection.
- `data/webxr.json` — curated WebXR experiences.
- `images/apps/` — app screenshots/logos, referenced by relative path from `data/apps.json`.
- `images/logo/` — shared branding assets.
- `ui/app.js`, `ui/styles.css` — the shared Alpine.js app logic and styling both sites load directly via `<script src="...">`/`<link href="...">`. This is the actual single source for filtering/search/card rendering — don't fork a local copy into either site repo. Per-site differences (branding text, tab layout, which `visibility` flag gates the app list) live in each site's own `index.html`; `app({ site: 'library' | 'catalogue' })` takes that as its only real config.
- `CONFLICTS.md` — record of how conflicting data was resolved when the two sites' old JSON files were merged. Not needed day-to-day, kept for history.

## Consuming this data

Both sites fetch JSON straight from a CDN mirror of this repo (jsdelivr), the same pattern they already use for Tailwind/Alpine:

```js
fetch('https://cdn.jsdelivr.net/gh/Lone-Star-XR-Lab/xr-app-data@main/data/apps.json')
```

No build step, no manual copying. In practice, jsdelivr's `@main` caching has been unreliable about picking up pushes on its own -- don't count on it refreshing within any predictable window. See "Cache-busting after a push" below for how this is actually kept fresh.

## Cache-busting after a push

Because `@main` is a mutable reference, jsdelivr (and browsers) can keep serving an old cached copy of a file indefinitely after you push a change. Two things make this survivable:

1. **`ui/app.js` and `ui/styles.css`** are loaded by each site with a `?v=...` query string on the `<script src="...">`/`<link href="...">` tag (e.g. `ui/app.js?v=2026-07-20-3`). Since a different query string is a different URL as far as both jsdelivr and the browser are concerned, bumping that version after any change to `ui/` guarantees a fresh fetch. Bump it in three places together: the `DATA_VERSION` constant near the bottom of `ui/app.js`, and the `?v=` on the `<script>`/`<link>` tags in both `xr-lab-library/index.html` and `xr-lab-catalogue/index.html`.
2. **`data/*.json`** is fetched by `ui/app.js` with that same `DATA_VERSION` appended as `?v=...`, so once `app.js` itself has loaded fresh (via #1), data changes come along with it automatically -- you don't need to separately version each data file.

If you push a change and it's still not showing up after bumping the version, jsdelivr's purge endpoint forces an immediate refresh for one specific URL: `https://purge.jsdelivr.net/gh/Lone-Star-XR-Lab/xr-app-data@main/<path>`. A plain browser refresh may also still show the old version if the browser itself cached the old URL -- hard refresh (Ctrl/Cmd+Shift+R) or an incognito window rules that out.

## Visibility model

Each app has a `visibility` object instead of being duplicated into separate lists:

```json
"visibility": { "library": true, "catalogue": false }
```

- `library: true` — shown on xr-lab-library. Apps currently approved and/or installed in the XR Lab.
- `catalogue: true` — shown on xr-app-catalogue. Anything in the XR ecosystem worth tracking, owned or not.

An app can be `true`/`true`, either alone, or neither (tracked here but not yet surfaced anywhere).

## Editing data

Edit `data/apps.json` directly (or the other data files) and push. Both sites will pick up the change automatically once jsdelivr's cache rolls over. There is currently no admin UI or validation script — that's a reasonable future addition once the schema stabilizes.
