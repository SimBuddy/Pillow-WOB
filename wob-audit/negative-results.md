# Negative results

Rejected and no-signal directions, recorded for transparency and to avoid dead ends.

| target | hypothesis | result | why rejected |
|---|---|---|---|
| Blend (ImageEnhance) | restructure float loop to vectorise | 0 gain | exact float32 semantics forbid a fixed-point replacement |
| point() RGB LUT | counted loop vectorises | 0 gain | per-pixel table gather has no plain-NEON gather |
| tobytes() | larger blocks reduce boundary overhead | slower | per-row packing dominates |
| global -O3 | vectorises byte loops | diagnostic only | not portable/upstream-acceptable |
| resample / box / gaussian blur | restructure kernels | no change | already heavily optimised |
| rgb2la (L24) | chunking helps | no change | compute-bound luminance |
| ImageChops multiply/screen/add/sub | dead-clip removal | not probed | division/clipping dominate |
| split/merge fusion | remove passes | n/a | would change the public API |
| ImageDraw | reduce Python/C boundary | ~1.3x, unverified | high complexity, modest gain |
| crop/paste | faster copy | ~1.0x | already near the copy bound |
| linear_gradient | hoist division | ~already optimal | already a C fill |

## Corrections made during the campaign

- An early report of a possible out-of-bounds write in the histogram was a false
  positive: Pillow stores RGB images as four-byte-packed pixels, so the write was in
  bounds. No security issue exists.
- Earlier speedup figures were partly inflated by a screen-off CPU frequency cap on the
  measurement device; the numbers above are the corrected full-speed measurements.
