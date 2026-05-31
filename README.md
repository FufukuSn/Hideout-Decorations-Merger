# Hideout Waypoint Offset Merger

A single-file, browser-based tool for **Path of Exile** hideout files (`.hideout`). It imports every doodad from one layout into another, re-anchoring all imported pieces to the target layout's **Waypoint** — so they keep their exact position relative to the Waypoint even though the Waypoint itself sits somewhere different in the new hideout.

No installation, no build step, no server. Open the HTML file in any modern browser and it runs entirely on your machine — **nothing is uploaded**.

---

## Why this exists

When you move a decorated section from one hideout to another, the second hideout's Waypoint is almost never in the same spot. Copying doodads by raw coordinates lands everything in the wrong place. This tool fixes that by treating the **Waypoint as a shared anchor**:

```
translation = targetWaypoint − sourceWaypoint
newDoodad.x = sourceDoodad.x + translation.x
newDoodad.y = sourceDoodad.y + translation.y
```

The target Waypoint never moves. Every imported doodad ends up the same distance and direction from the Waypoint that it had in the source layout.

---

## Screenshots

> Replace these with your own captures, or use the images in [`screenshots/`](screenshots/).

### Start screen
![Start screen](screenshots/01-empty.png)

### Files loaded — source and target detected
![Files loaded](screenshots/02-files-loaded.png)

### Offset preview
![Offset preview](screenshots/03-preview.png)

<!--
  Add more screenshots here as needed, e.g.:
  ### Merged result
  ![Merged result](screenshots/04-result.png)
-->

---

## Features

- **Two file selectors** (click or drag-and-drop) — a **source** and a **target**.
- **Waypoint-anchored offset** — imported doodads keep their exact offset from the Waypoint; the target Waypoint stays fixed.
- **Preserves duplicate-named entries** — hideout files repeat decoration names (e.g. `"Faridun Ropes"` hundreds of times). A standard `JSON.parse` collapses these into one; this tool uses a custom duplicate-preserving parser **and** serializer so every single entry survives.
- **Preserves rotation & variation** — each doodad's `r` (rotation) and `fv` (variation/flags) are carried over unchanged.
- **Two merge modes:**
  - **Replace** (default) — the target's doodads are replaced by the re-anchored source set.
  - **Append** — keep the target's own doodads and add the source set on top. The Waypoint is never duplicated.
- **Offset preview** — inspect the first 40 doodads (source x/y → new x/y → offset from Waypoint) before downloading.
- **Format-faithful output** — keeps the UTF-8 BOM and indentation the game expects, and emits valid JSON.
- **100% client-side** — no network calls, no telemetry.

---

## Usage

1. Open `index.html` in a browser (or visit the hosted version).
2. **Source** — choose the file whose doodads you want to import (e.g. your dreadnought layout). *All* doodads come from here.
3. **Target** — choose the file whose Waypoint and metadata stay fixed (e.g. your apex layout).
4. (Optional) Tick **"Also keep the target file's own doodads"** to append instead of replace.
5. Click **Preview offsets** to sanity-check, then **Merge & download**.
6. The downloaded file is named `merged_<targetname>.hideout`.

---

## How it works (technical)

| Step | What happens |
|------|--------------|
| Parse | A hand-written JSON parser builds an AST that keeps duplicate object keys (native `JSON.parse` would drop them). |
| Locate anchors | Reads the `"Waypoint"` doodad from both files. |
| Translate | Computes `dx, dy = targetWaypoint − sourceWaypoint` and adds it to each imported doodad's `x`/`y`. |
| Rebuild | Replaces (or appends to) the target's `doodads`, keeping the target's top-level metadata (`version`, `language`, `hideout_name`, `hideout_hash`). |
| Serialize | A matching serializer writes the AST back out with the BOM and indentation preserved. |

The Waypoint itself is written from the **target** file verbatim, guaranteeing it never shifts.

### Doodad entry shape

```json
"Stash": {
  "hash": 3230065491,
  "x": 388,
  "y": 888,
  "r": 16387,
  "fv": 0
}
```

Only `x` and `y` are modified on import. `hash`, `r`, and `fv` are preserved.

---

## File format notes

`.hideout` files are UTF-8 (with BOM) JSON. The top level looks like:

```json
{
  "version": 1,
  "language": "English",
  "hideout_name": "Overgrown Apex Hideout",
  "hideout_hash": 31541,
  "doodads": { ... }
}
```

The `doodads` object may legitimately contain **repeated keys** for decorations placed multiple times. This tool is built specifically to handle that.

---

## Project structure

```
.
├── index.html        # the entire tool (HTML + CSS + JS, no dependencies)
├── LICENSE           # GNU GPL v3.0
├── README.md         # this file
└── screenshots/      # example images used above
```

---

## License

Licensed under the **GNU General Public License v3.0**. See [`LICENSE`](LICENSE) for the full text.

This program is free software: you can redistribute it and/or modify it under the terms of the GPL as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version. It comes with **ABSOLUTELY NO WARRANTY**.

---

## Disclaimer

This is an unofficial, fan-made utility. Path of Exile, the `.hideout` format, and all in-game asset names are the intellectual property of **Grinding Gear Games**. This project is not affiliated with, endorsed by, or sponsored by GGG.
