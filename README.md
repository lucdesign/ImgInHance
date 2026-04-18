# ImgInHance

**Tampermonkey userscript — inline auto-levels image enhancement for any webpage**

ImgInHance analyses the histogram of every image on a page and applies per-channel auto-levels correction to images whose tonal range is suboptimal (flat, low-contrast, faded). The enhancement runs entirely in the browser via Canvas API — no server, no upload, no external dependency.

---

## How it works

1. After the page loads, every `<img>` element is inspected.
2. The pixel data is analysed: per-channel histograms are built, clipping points are calculated.
3. If the image is judged "dull" (combined channel stretch factor exceeds the quality threshold), auto-levels correction is applied via a LUT and the result is swapped in as a blob URL.
4. New images added dynamically to the page (SPAs, lazy-loading, infinite scroll) are picked up automatically via a MutationObserver.
5. Cross-origin images that would cause a canvas CORS error are fetched via `GM_xmlhttpRequest` and processed through a proxy blob.

The original `src` and `srcset` are preserved. The enhancement is fully reversible.

---

## Toggle

A floating button (✦) appears in the top-right corner of every page.

| State | Appearance                       |
| ----- | -------------------------------- |
| ON    | Blue background, full opacity    |
| OFF   | Grey background, reduced opacity |

Click to toggle. The state persists across page loads via `localStorage`.

---

## Installation

1. Install [Tampermonkey](https://www.tampermonkey.net/) for your browser.
2. Open [`ImgInHance.user.js`](ImgInHance.user.js) — Tampermonkey will detect the userscript header and offer to install it.
3. Confirm. Done.

> Works on any `*://*/*` URL by default. No configuration needed.

---

## Technical details

| Parameter       | Value              | Notes                                                                              |
| --------------- | ------------------ | ---------------------------------------------------------------------------------- |
| `MIN_QUALITY`   | `0.9`              | Images are enhanced only if the combined channel factor drops below this threshold |
| `CLIP_PERCENT`  | `0.05 %`           | Shadow/highlight clipping tolerance for histogram endpoints                        |
| `MIN_DIMENSION` | `32 px`            | Images smaller than 32 × 32 px are skipped                                         |
| Output format   | JPEG, quality 0.92 | Via `toBlob` / `convertToBlob` with ObjectURL                                      |

Correction is applied per RGB channel independently (not luminance-only), so colour casts are also resolved when the channel ranges differ significantly.

---

## Requirements

- Tampermonkey (Chrome, Firefox, Safari, Edge)
- `GM_xmlhttpRequest` + `@connect *` permission — granted automatically on install, required for cross-origin images

---

## License

MIT — © [lucdesign](https://luc.at)
