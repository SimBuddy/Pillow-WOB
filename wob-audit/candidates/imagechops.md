# Candidate — ImageChops difference/lighter/darker

    TARGET=ImagingChopLighter/Darker/Difference
    STATUS=READY_FOR_PR
    FILES=src/libImaging/Chops.c
    MECHANISM=remove provably-dead clipping
    HEADROOM=difference ~1.8x; lighter/darker ~1.0x
    CHANGE=use the no-clip CHOP2 loop for the three bounded operations
    CORRECTNESS=324-case differential (0 mismatches), test_imagechops, full suite, UBSan
    PERFORMANCE=difference 1.72x/1.80x/1.81x (256/1080p/4K)
    PORTABILITY=pure C; reuses the existing CHOP2 macro
    MAINTENANCE_COST=low
    INTERACTIONS=independent
    BRANCH=candidate/imagechops-bounds
    COMMIT=c85c5595a
    PROPOSED PR TITLE=Remove unnecessary clipping from ImageChops operations

---

The maximum, minimum and absolute difference of two bytes are always within 0..255, so
the clipping branches inherited from the `CHOP` macro are never taken. Using the no-clip
`CHOP2` loop removes them and lets `ImageChops.difference` run ~1.8x faster.
