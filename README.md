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

Both sites fetch JSON straight from a CDN mirror of this repo (jsdelivr), the same pattern they already use for Tailwind/Alpine. **As of 2026-07-20, every jsdelivr URL used in this project is pinned to a specific commit SHA rather than `@main`** -- e.g.:

```js
fetch('https://cdn.jsdelivr.net/gh/Lone-Star-XR-Lab/xr-app-data@aaf69fd14941778d8416ad0e80a93f968eb3ef59/data/apps.json')
```

`@main` was tried first and abandoned: jsdelivr's edge caching for that mutable ref proved unreliable in a way that didn't self-heal -- purge requests reported `"status": "finished"` but repeat purges for the same URL returned the *same old job record* instead of re-checking, and stale content kept being served even in incognito windows on multiple browsers. A commit SHA is immutable, content-addressed data, so a SHA-pinned URL is correct on its very first fetch, permanently, with no cache-busting needed. The cost: after every push, the pinned SHA has to be bumped everywhere it's referenced. See "Deploying a change" below for the exact steps.

## Deploying a change

There is no `@main`/`?v=` cache-busting to think about anymore -- instead, every push means updating a SHA reference. In order:

1. Edit `data/*.json`, `ui/app.js`, `ui/styles.css`, etc. as needed and commit/push this repo (`xr-app-data`).
2. Get the resulting commit's full SHA (`git log -1 --format=%H` after the push, or copy it from GitHub).
3. If `ui/app.js` or `ui/styles.css` themselves changed, update `DATA_CDN_BASE` and `ASSET_CDN_BASE` near the bottom of `ui/app.js` to that new SHA, then commit/push again (this is a second commit, so grab its SHA too -- that's the one everything downstream should point to).
4. Update the `<script src="...ui/app.js">` and `<link href="...ui/styles.css">` tags in both `xr-lab-library/index.html` and `xr-lab-catalogue/index.html` to reference that same final SHA, and commit/push both site repos.

If a fetch ever needs force-refreshing during this process (e.g. testing before the SHA is finalized), jsdelivr's purge endpoint is still available: `https://purge.jsdelivr.net/gh/Lone-Star-XR-Lab/xr-app-data@main/<path>` (note this only ever applies to `@main`, which nothing should be pointing at anymore in steady state). A plain browser refresh can also still show an old *SHA-pinned* response if the browser itself cached that exact URL from before -- but since each SHA is unique per commit, this should only matter while testing the same SHA repeatedly; a hard refresh (Ctrl/Cmd+Shift+R) or incognito window rules it out.

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
