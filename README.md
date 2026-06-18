# Talko-content

Remote content assets for the [Talk](https://github.com/sashavoloshanov) iOS app (bundle ID `com.svoloshanov.Talk`).

This repository exists so that visual content can be updated and extended without shipping a new app build or growing the app binary. The app downloads files from here on demand and caches them on disk.

---

## Why this repo is public

Files here are served through [jsDelivr](https://www.jsdelivr.com/), a free CDN that mirrors public GitHub repositories. The app fetches assets via:

```
https://cdn.jsdelivr.net/gh/sashavoloshanov/Talko-content@main/{path}
```

The repo must stay **public** for this CDN URL to work without authentication. Don't commit anything sensitive here — treat it purely as a static asset bucket.

---

## Structure

```
Talko-content/
└── Badges/
    ├── badge_{subcategoryId}_10.png
    ├── badge_{subcategoryId}_30.png
    └── badge_{subcategoryId}_50.png
```

### Badges/

Achievement badge artwork, awarded in-app per subcategory at **10 / 30 / 50** answered questions.

| Field | Value |
|---|---|
| Format | `.png` |
| Naming | `badge_{subcategoryId}_{threshold}.png` |
| Thresholds | `10`, `30`, `50` |
| `subcategoryId` | Must exactly match the subcategory `id` used in the app's content JSON (`couple.json`, `family.json`, `friends.json`) |

Example: the `couple_romance` subcategory needs three files —
`badge_couple_romance_10.png`, `badge_couple_romance_30.png`, `badge_couple_romance_50.png`.

---

## How the app consumes this

The app's `BadgeImageClient` downloads each badge the first time it's shown, then caches it on disk (`Caches/BadgeImages/`) so it's never re-downloaded after that. There's no app-side fallback for missing files — if a badge image isn't here, the app shows a locked-style placeholder instead.

---

## Adding a new badge

1. Add the new subcategory to the relevant `{category}.json` in the main Talk repo (both `en.lproj/` and `uk.lproj/`).
2. Export three PNGs for the new subcategory at the 10 / 30 / 50 thresholds, named exactly as above.
3. Commit and push to `main`.
4. jsDelivr caches files at the edge and may serve a stale version for a while. To force an immediate refresh after publishing, purge each file:

   ```
   https://purge.jsdelivr.net/gh/sashavoloshanov/Talko-content@main/Badges/badge_{subcategoryId}_{threshold}.png
   ```

   (Open the URL once per file, or `curl` it — no auth needed.)

---

## Guidelines for badge artwork

- Square canvas, transparent background.
- Keep file sizes reasonable — these are downloaded over cellular on first view.
- Visually distinct between the 10 / 30 / 50 tiers of the same subcategory (e.g. increasing detail, color, or a tier frame) so progression feels rewarding.
