# AUTOMATA — Creative Agency Website

A single-page, tech-style, minimalist website for a creative agency. There is no downward scrolling: each scroll swaps the section, and the background video moves along with it.

## Features

- **Fullpage sections** — 5 sections (Hero, Services, Process, Impact, Contact) switch with a fade + slide transition.
- **Scroll-controlled video** — the video position follows the active section: Hero sits at the start, Contact at the end.
- **3D icons in empty space** — a rotating 3D object lives inside the video, appears on the side with no text, and disappears on full-width sections.
- **Overlay menu** — open with MENU, close with the CLOSE button or Esc.
- **Responsive** — single-column layout on small screens, and the 3 cards always stay in one row.
- **Fallback** — if the video fails to load, the background switches to an animated dot-wave canvas.

## Files

| File | Purpose |
|---|---|
| `protec.html` | The complete website (HTML, CSS, JS, and embedded video). Can be renamed to `index.html`. |
| `generate_video.py` | Script that generates the background video. |
| `README.md` | This document. |

## Running

Open `protec.html` directly in a browser. No build step or server needed. Fonts load from Google Fonts, so an internet connection is required (system font fallbacks are included).

## Navigation

| Action | Result |
|---|---|
| Mouse wheel / trackpad scroll | Change section |
| Swipe up/down (mobile) | Change section |
| `↓` `PageDown` `Space` / `↑` `PageUp` | Next / previous section |
| Menu, EXPLORE button, LET'S TALK | Jump to a specific section (`data-go="n"`) |

A ~1 second lock between transitions prevents skipping two sections at once.

## Section layout

| # | Section | Text | Video icon |
|---|---|---|---|
| 0 | Hero | left | right (octahedron) |
| 1 | Services | right (`class="s r"`) | left (cube) |
| 2 | Process | full width (`class="s w"`) | none |
| 3 | Impact | left | right (torus) |
| 4 | Contact | right (`class="s r"`) | left (icosahedron) |

## Customization

**Text and content** — edit directly inside each `<section class="s ...">` tag in the HTML file. The current copy and numbers are placeholders.

**Colors and fonts** — CSS variables at the top of the stylesheet:

```css
:root{--bg:#000;--fg:#fff;--mut:#c4c4c4;--line:rgba(255,255,255,.12);--ac:#2f6bff;
      --d:"Orbitron",...;--b:"Inter",...}
```

`--ac` is the blue accent color (SVG icons, the dot in the hero logo).

**Section layout** — add class `r` for right-aligned text, or `w` for a full-width section.

## Replacing the background video

The video is embedded as a data URI in `window.BG_VIDEO` (the first script in the HTML file).

1. Prepare a new video. For smooth scrubbing use at least 1280×720, 150+ frames, and a keyframe on every frame:
   ```bash
   ffmpeg -i input.mp4 -c:v libx264 -g 1 -crf 26 -pix_fmt yuv420p -movflags +faststart -an bg.mp4
   ```
2. Convert it to base64 and paste it as `window.BG_VIDEO="data:video/mp4;base64,..."`.

Video time is computed as `section / (sectionCount − 1) × (duration − 0.08)`. The first section lands at the start of the video and the last section at the end.

**Regenerating the bundled video**

```bash
pip install numpy opencv-python
python3 generate_video.py   # requires ffmpeg; output: bg.mp4
```

Icon settings live in the `SEC` dictionary in the script: `{section_index: (shape, x_position, scale)}`. An x position of `0.76` places the icon on the right and `0.25` on the left. Sections missing from the dictionary show no icon. If a section's layout changes, update `SEC` and re-render the video.

## Adding or removing sections

1. Add or remove `<section>` blocks and menu items (`data-go`).
2. In `generate_video.py`, the expression `f=t*4` assumes 5 sections; change `4` to `sectionCount − 1`, then adjust `SEC`.
3. Re-render the video and embed it again.

## Notes

- The file is about 7 MB because the video is embedded; the artifact hosting limit is 16 MB.
- On phones, the video icon sits behind the text, so the video is darkened to keep the text readable.
- All visuals are generated procedurally (no pre-made 3D assets).
