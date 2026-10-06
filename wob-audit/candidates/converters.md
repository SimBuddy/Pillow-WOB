# Candidate — L mode conversions (l2rgb)

    TARGET=l2rgb (L -> RGB/RGBA/RGBX/LA)
    STATUS=READY_FOR_PR
    FILES=src/libImaging/Convert.c
    MECHANISM=32-bit byte broadcast
    HEADROOM=~4.7x at 1080p
    CHANGE=broadcast each greyscale byte into three channels + 255; 4 pixels/iteration
    CORRECTNESS=66-case differential (0 mismatches), full suite, UBSan
    PERFORMANCE=4.1x/4.7x/4.4x (256/1080p/4K)
    PORTABILITY=UINT32 + memcpy + WORDS_BIGENDIAN; no intrinsics
    MAINTENANCE_COST=low
    INTERACTIONS=independent
    BRANCH=candidate/converter
    COMMIT=1a70408f9
    PROPOSED PR TITLE=Speed up conversions from L mode

---

`l2rgb` writes the same greyscale byte into the three colour channels and 255 into the
fourth byte, one byte at a time. The change processes four pixels per iteration with
32-bit stores.
