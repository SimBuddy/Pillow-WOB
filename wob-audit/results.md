# Results

Five candidates reached READY_FOR_PR. All are validated by the full Pillow test suite
(5197 passed, 0 failed) and UBSan, and by byte-exact differential corpora.

Speedups are measured on ARM64 at 1080p unless stated; 4K figures are lower because
those operations become memory-bandwidth-bound.

| candidate | 256x256 | 1920x1080 | 3840x2160 |
|---|---|---|---|
| RGB -> RGBA | 5.9x | 6.7x | 3.3x |
| RGBA -> RGB | 5.9x | 6.9x | 3.4x |
| L -> RGB | 4.1x | 4.7x | 4.4x |
| ImageChops.difference | 1.7x | 1.8x | 1.8x |
| RGB histogram | 2.9x | 3.3x | 3.3x |
| ImageOps.equalize (RGB) | 1.6x | 2.1x | 2.1x |

No tiny-image regression was observed for any candidate (all ~1.0x at 1-32 px).
