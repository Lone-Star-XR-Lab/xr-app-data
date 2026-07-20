# xr-app-data

Single source of truth for [xr-lab-library](https://github.com/Lone-Star-XR-Lab/xr-lab-library) and [xr-app-catalogue](https://github.com/Lone-Star-XR-Lab/xr-lab-catalogue). Both sites fetch data and images from this repo at runtime — nothing is copied between repos.

## Contents

- `data/apps.json` — every XR app, merged from both sites' former local copies. See `data/schema.json` for field definitions.
- `data/videos.json` — curated YouTube/360 video collection.
- `data/webxr.json` — curated WebXR experiences.
- `images/apps/` — app screenshots/logos, referenced by relative path from `data/apps.json`.
- `images/logo/` — shared branding assets.
- `CONFLICTS.md` — record of how conflicting data was resolved when the two sites' old JSON files were merged. Not needed day-to-day, kept for history.

## Consuming this data

Both sites fetch JSON straight from a CDN mirror of this repo (jsdelivr), the same pattern they already use for Tailwind/Alpine:

```js
fetch('https://cdn.jsdelivr.net/gh/Lone-Star-XR-Lab/xr-app-data@main/data/apps.json')
```

No build step, no manual copying. jsdelivr caches for a few minutes after a push; use the `@latest` tag or jsdelivr's purge endpoint if you need to force an immediate refresh after publishing a change.

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
# xr-app-data
