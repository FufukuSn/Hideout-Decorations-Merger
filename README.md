## Hideout Decorations Merger 
https://fufukusn.github.io/Hideout-Decorations-Merger/

A single-file, browser-based tool for **Path of Exile** hideout files (`.hideout`). It imports every doodad from one layout into another, re-anchoring all imported pieces to the target layout's **Waypoint** — so they keep their exact position relative to the Waypoint even though the Waypoint itself sits somewhere different in the new hideout for your gooning pleasure.

Open the HTML file in any modern browser (tested chromium, firefox, Internet explorer) and it runs entirely on your machine — **nothing is uploaded**.

---

## Screenshots



## How to use

- **Two file selectors** (click or drag-and-drop) — a **source** and a **target**.
- **Waypoint-anchored offset** — imported doodads keep their exact offset from the Waypoint; the target Waypoint stays fixed.
- **Preserves rotation & variation** — each doodad's `r` (rotation) and `fv` (variation/flags) are carried over unchanged.
- **Two merge modes:**
  - **Replace** (default) — the target's doodads are replaced by the re-anchored source set.
  - **Append** — keep the target's own doodads and add the source set on top.
- **Offset preview** — inspect the first 40 doodads (source x/y → new x/y → offset from Waypoint) before downloading.



## Usage

1. Open `index.html` in a browser (or visit the hosted version).
2. **Source** — choose the file whose doodads you want to import (e.g. your dreadnought layout). *All* doodads come from here.
3. **Target** — choose the file whose Waypoint and metadata stay fixed (e.g. your apex layout).
4. (Optional) Tick **"Also keep the target file's own doodads"** to append instead of replace.
5. Click **Preview offsets** to sanity-check, then **Merge & download**.
6. The downloaded file is named `merged_<targetname>.hideout`.



## How it works (technical)

| Step | What happens |
|------|--------------|
| Parse | A hand-written JSON parser builds an AST that keeps duplicate object keys (native `JSON.parse` would drop them). |
| Locate anchors | Reads the `"Waypoint"` doodad from both files. |
| Translate | Computes `dx, dy = targetWaypoint − sourceWaypoint` and adds it to each imported doodad's `x`/`y`. |
| Rebuild | Replaces (or appends to) the target's `doodads`, keeping the target's top-level metadata (`version`, `language`, `hideout_name`, `hideout_hash`). |
| Serialize | A matching serializer writes the AST back out with the BOM and indentation preserved. |

The Waypoint itself is written from the **target** file verbatim.

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




## License

Licensed under the **GNU General Public License v3.0**. See [`LICENSE`](LICENSE) for the full text.



## Disclaimer

This is an unofficial, fan-made utility. Path of Exile, the `.hideout` format, and all in-game asset names are the intellectual property of **Grinding Gear Games**. This project is not affiliated with, endorsed by, or sponsored by GGG.
