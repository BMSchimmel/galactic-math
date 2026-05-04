# Performance & Token-Efficiency Notes

## index.html Size Snapshot (May 2026)

| Section | Lines | Size |
|---|---|---|
| CSS (`<style>`) | 1,516 | ~36 KB |
| HTML body | 231 | ~13 KB |
| JS (`<script>`) | 1,302 | ~43 KB |
| **Total** | **3,083** | **~90 KB** |

## Recommendations

### 1. Extract CSS → `assets/css/style.css`

**Savings:** Removes ~36 KB / 1,516 lines from index.html.

**Why it helps:**
- When working on game logic, Claude reads all 3,083 lines even though the CSS is irrelevant. Extracting it lets Claude load `game.js` (or the HTML) without ingesting the full stylesheet.
- Browsers cache external files between page reloads; inline styles are re-parsed every load.

**How:** Replace the `<style>…</style>` block with `<link rel="stylesheet" href="assets/css/style.css">`.

---

### 2. Extract JS → `assets/js/game.js`

**Savings:** Removes ~43 KB / 1,302 lines from index.html.

**Why it helps:**
- The JS is the most actively edited section. Keeping it in a dedicated file lets Claude (and human editors) navigate to it without opening the full monolith.
- Separating it enables section-level reads (e.g. "just read the audio engine") without pulling in HTML and CSS.

**How:** Replace the `<script>…</script>` block with `<script src="assets/js/game.js"></script>`. No module bundler needed.

---

### 3. After extraction, index.html drops to ~13 KB

The remaining HTML skeleton (head metadata, Google Fonts link, body markup) is the only piece that rarely needs editing. It stays in `index.html` as the entry point.

---

## Sub-module candidates inside game.js

Once JS is extracted, the audio engine (lines 1,872–2,068 in the current index.html, ~196 lines) is the strongest candidate for a further split into `assets/js/audio.js`. It is entirely self-contained, well-documented in `docs/audio-engine.md`, and almost never changes alongside quiz or UI code.

The animation helpers (spaceship flyby, ring shockwave, comet streaks, hyperspace jump, lines ~2,068–2,359) are a secondary candidate for `assets/js/animations.js`.

---

## What NOT to split

- **HTML body** — at 231 lines it is already minimal.
- **CLAUDE.md** should be updated to reflect the new file layout if these extractions are accepted. The current "one file" constraint was written when index.html was smaller.
