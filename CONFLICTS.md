# Merge conflict report

Generated while merging `xr-lab-library/app_library.json` (73 apps) and `xr-lab-catalogue/app_library.json` (83 apps) into a single `data/apps.json` (103 unique apps, matched by title).

Default policy: for `approved`/`installed`, the library's tracked value wins (that's where lab status is authoritative). For every other field, if both repos had a non-empty value and they differed, the library's version was kept as canonical and the conflict was logged below. If you disagree with any resolution, tell me and I'll adjust `data/apps.json` directly — nothing here is final yet.

## Needs your judgment call

**"First Contact"** — these look like two different app *versions*, not just wording drift. Library describes the current Meta Quest release (Quest/Quest 2/Quest 3/Quest Pro, `meta.com` link, approved+installed). Catalogue describes what reads like a stale entry for the original Oculus Rift CV1 PCVR version (`oculus.com` link, tagged "Legacy", explicitly "not available on standalone Quest"). I merged these into one record using the library's current Quest data and dropped the Rift CV1 details. If the Rift CV1 version is worth keeping as its own historical/PCVR catalogue entry, tell me and I'll split it into a second record (e.g. `first-contact-rift-cv1`).

**"The Body VR: Journey Inside a Cell"** — library's `logo` pointed at `images/apps/the-body-vr-journey-inside-a-cell.jpg`, a file that doesn't exist in either repo. Catalogue's `images/apps/the-body-vr.jpg` does exist and both copies are byte-identical. I used catalogue's path instead of blindly preferring library here, since library's was a broken link.

## Minor editorial conflicts (kept library's wording)

| App | Field | Library value | Catalogue value |
|---|---|---|---|
| First Contact | tags | Game, Tutorial, Onboarding | Game, Tutorial, Onboarding, Legacy |
| First Contact | educational_uses | "...basic VR mechanics on standalone Quest headsets." | "...Useful historically or in PCVR contexts where Rift is available." |
| First Contact | notes | "Free official Quest onboarding experience..." | "Designed for Oculus Rift CV1 + Touch controllers; legacy app..." |
| First Contact | devices | Quest, Quest 2, Quest Pro, Quest 3 | Oculus Rift CV1 |
| First Contact | platforms | Meta Quest | Oculus Rift (PCVR) |
| First Contact | link | meta.com/experiences/2188021891257542 | oculus.com/experiences/rift/1262165400515499 |
| Gods of Gravity | categories | Games, Student Life, Collaboration | Games, STEM, Student Life |
| Tablecraft | content_notes | "Cartoon science humor; includes radiation and chemistry references." | "Cartoon humor; references to radiation and lab mishaps." |
| Tablecraft | tags | Science, Chemistry, Physics, Crafting, Education, Sandbox | Science, Chemistry, Physics, Crafting, Education |
| Tablecraft | educational_uses | "...gamifies atomic structure, isotopes, and nuclear reactions..." | "...gamifies the periodic table, atomic structure, and isotopes..." |
| Tablecraft | notes | "Currently free in Early Access..." | "Currently free in early access; full release may be paid..." |
| Tablecraft | description | "...discover new elements, isotopes, and over 900 possible recipes..." | "...discover elements, isotopes, and over 900 recipes..." |

(First Contact's six-field conflict is the same "different version" issue flagged above, listed here too for completeness.)

## Known data gaps in the merged schema

- `developer` — not present in either source file. Every record has `developer: null` until backfilled.
- `videos` (per-app demo/trailer URLs) — not present in either source file. Every record has `videos: []`.
- These are separate from `data/videos.json`, which is the existing curated YouTube video collection (identical in both repos, 34 entries, merged with zero conflicts).
- `data/webxr.json` — identical in both repos (5 entries), merged with zero conflicts.
