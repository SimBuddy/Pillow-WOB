# Candidate — RGB <-> RGBA conversions

    TARGET=rgb2rgba and rgba2rgb
    STATUS=READY_FOR_PR
    FILES=src/libImaging/Convert.c
    MECHANISM=4-pixel 32-bit shuffle
    HEADROOM=~6.7x (RGB->RGBA) / ~6.9x (RGBA->RGB) at 1080p
    CHANGE=chunked loop + endianness mask + scalar tail; rgba2rgb delegates to rgb2rgba
    CORRECTNESS=1224-case differential (0 mismatches), full suite, UBSan
    PERFORMANCE=5.9x/6.7x/3.3x (256/1080p/4K); no tiny regression
    PORTABILITY=UINT32 + memcpy + WORDS_BIGENDIAN; no intrinsics
    MAINTENANCE_COST=low
    INTERACTIONS=independent
    BRANCH=wob/rgb2rgba-performance
    COMMIT=4a148678a
    PROPOSED PR TITLE=Speed up RGB and RGBA conversions

---

Pillow stores RGB-family pixels as four bytes per pixel (three colour bytes plus a
padding/alpha byte). Both `rgb2rgba` and `rgba2rgb` copy the three colour bytes and
write 255 into the fourth byte. The previous implementation did this a byte at a time;
the change processes four pixels per iteration with 32-bit loads and stores so the
compiler emits vector code.
