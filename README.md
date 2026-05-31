## Hideout Decorations Merger 
https://fufukusn.github.io/Hideout-Decorations-Merger/

A single-file, browser-based tool for **Path of Exile** hideout files (`.hideout`). It imports every doodad from one layout into another, re-anchoring all imported pieces to the target layout's **Waypoint** — so they keep their exact position relative to the Waypoint even though the Waypoint itself sits somewhere different in the new hideout for your ~~gooning~~ pleasure. 

Open the HTML file in any modern browser (tested chromium, firefox, Internet explorer) and it runs entirely on your machine — **nothing is uploaded**.

Should work with any language, tested on English, Russian and Chinese including source and target files. The Waypoint is located by its internal hash, if that hash ever changes it should still work with English source/targets that you can manually make if you have required hideouts in game.

Should techincally work on path of exile 1, even if waypoint internal hash is different, it should pick up 'Waypoint' from english loc file, but I did not test it nor want to.

While the tool allows you to import any artistic composition from any eligible (`.hideout`) file, it **does not unlocks it on your account or spawns decorations you do not own.**



## Screenshots

![Screenshot 1](https://i.imgur.com/cGo7Qe8.jpeg)
![Screenshot 2](https://i.imgur.com/eoNiNXg.jpeg)

## How to use

- **Two file selectors** (click or drag-and-drop) — a **source** and a **target**.
- **Waypoint-anchored offset** — imported doodads keep their exact offset from the Waypoint; the target Waypoint stays fixed.
- **Preserves rotation & variation** — each doodad's `r` (rotation) and `fv` (variation/flags) are carried over unchanged.
- **Language-independent** — Waypoint found by hash, so non-English hideout files (Russian, Chinese, etc.) work out of the box.
- **Two merge modes:**
  - **Replace** (default) — the target's doodads are replaced by the re-anchored source set.
  - **Append** — keep the target's own doodads and add the source set on top.
- **Cluster recentering** (optional) — pull far-away decoration groups back toward the Waypoint so they stay inside the in-game placement bounds, without distorting the groups themselves.
- **Offset preview** — inspect the first 40 doodads (source x/y → new x/y → offset from Waypoint) before downloading.



## Usage

1. Open `index.html` in a browser (or visit the hosted version).
2. **Source** — choose the file whose doodads you want to import (e.g. your dreadnought layout). *All* decorations come from here.
3. **Target** — choose the file whose Waypoint and metadata stay fixed (e.g. your apex layout).
4. (Optional) Tick **"Also keep the target file's own decorations"** to append instead of replace.
5. (Optional) Tick **"Recenter clusters near the Waypoint"** if your source decorations sit far from its Waypoint and would otherwise land outside the new hideout's bounds. Adjust the **Max distance from Waypoint** field if needed (default 175).
6. Click **Preview offsets** to sanity-check, then **Merge & download**.
7. The downloaded file is named `merged_<targetname>.hideout`.


## Cluster recentering

Some layouts place decorations far away from their Waypoint. When those offsets are re-applied to a different hideout, the imported pieces can land **outside the new hideout's placement bounds** and fail to load. Recentering fixes this while keeping your builds intact.

- **What's a cluster?** Any decorations where each one is within **5 units** (per axis) of another chain together. A cluster moves as one **rigid group**.
- **What recentering does:** when enabled, each cluster's center is pulled toward the Waypoint by a single uniform scale factor, just enough so the farthest cluster center fits inside your chosen **max distance**. Compaction only happens if something actually exceeds that distance, otherwise nothing moves.
- **Per-axis radius:** the max-distance limit applies to each cluster's *center* on each axis (Chebyshev).

## How it works (technical)

| Step | What happens |
|------|--------------|
| Parse | A hand-written JSON parser builds an AST that keeps duplicate object keys (native `JSON.parse` would drop them). |
| Locate anchors | Reads the `"Waypoint"` doodad from both files. |
| Translate | Computes `dx, dy = targetWaypoint − sourceWaypoint` and adds it to each imported doodad's `x`/`y`. |
| Recenter (optional) | Groups imported doodads into clusters (connected components, ≤5 units apart), scales each cluster's center toward the Waypoint to fit the max distance, and rigidly shifts each cluster. |
| Rebuild | Replaces (or appends to) the target's `doodads`, keeping the target's top-level metadata (`version`, `language`, `hideout_name`, `hideout_hash`). |
| Serialize | A matching serializer writes the AST back out with the BOM and indentation preserved. |

The Waypoint itself is written from the **target** file verbatim.

## License

Licensed under the **GNU General Public License v3.0**. See [`LICENSE`](LICENSE) for the full text.



## Disclaimer

This is an unofficial, fan-made utility. Path of Exile, the `.hideout` format, and all in-game asset names are the intellectual property of **Grinding Gear Games**. This project is not affiliated with, endorsed by, or sponsored by GGG.
