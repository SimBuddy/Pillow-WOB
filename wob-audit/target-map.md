# Target map

Surveyed subsystems and their terminal classification.

## Conversion (src/libImaging/Convert.c)

| target | mechanism | outcome |
|---|---|---|
| rgb2rgba / rgba2rgb | 4-pixel 32-bit shuffle | READY_FOR_PR (~6.7x) |
| l2rgb (L -> colour) | 32-bit byte broadcast | READY_FOR_PR (~4.7x) |
| rgb2la (L24 luminance) | compute-bound | NO_SIGNAL |
| other colorspace (cmyk/hsv/ycbcr) | compute-bound | NO_SIGNAL |

## Channel operations (src/libImaging/Chops.c)

| target | mechanism | outcome |
|---|---|---|
| difference | dead-clip removal | READY_FOR_PR (~1.8x) |
| lighter/darker | dead-clip removal | NO_SIGNAL (compiler already removes the clip) |
| multiply/screen/add/subtract | division/clip | NO_SIGNAL |

## Statistics (src/libImaging/Histo.c)

| target | mechanism | outcome |
|---|---|---|
| 3-band histogram | drop discarded pad byte | READY_FOR_PR (~3.3x) |

## Core (src/libImaging/Copy.c)

| target | mechanism | outcome |
|---|---|---|
| zero-width copy | guard memcpy(NULL,0) | CORRECTNESS_FINDING + READY_FOR_PR |

## Rejected / no signal (from the full campaign)

Blend vectorisation (exact float semantics), point() gather (no plain-NEON gather),
tobytes block-size tuning, global -O3, resample/box/gaussian (already optimised),
ImageDraw, split/merge, crop/paste, linear_gradient.

See `negative-results.md` for detail.
