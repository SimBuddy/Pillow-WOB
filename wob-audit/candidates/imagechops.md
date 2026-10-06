# Candidate — ImageChops.difference

    TARGET=ImagingChopDifference
    STATUS=READY_FOR_PR
    FILES=src/libImaging/Chops.c
    MECHANISM=remove provably-dead clipping
    HEADROOM=difference ~1.8x
    CHANGE=use the no-clip CHOP2 loop for ImageChops.difference
    CORRECTNESS=324-case differential (0 mismatches), test_imagechops, full suite, UBSan
    PERFORMANCE=difference 1.71x/1.80x/1.81x (256/1080p/4K)
    PORTABILITY=pure C; reuses the existing CHOP2 macro
    MAINTENANCE_COST=low
    INTERACTIONS=independent
    BRANCH=candidate/imagechops-difference
    COMMIT=5c116c7eb
    PROPOSED PR TITLE=Speed up ImageChops.difference()

---

The absolute difference of two bytes is always within 0..255, so the clipping branches
that `ImagingChopDifference` inherited from the `CHOP` macro are never taken. Using the
no-clip `CHOP2` loop removes those branches and makes `ImageChops.difference` ~1.8x
faster.

## Scope note (lighter / darker)

The original WOB investigation also examined `ImagingChopLighter` and
`ImagingChopDarker`, whose results (max/min of two bytes) are likewise always within
0..255. Applying the same no-clip transformation was tested and found exact, but
produced **no measurable speedup** (the compiler already proves those results are in
range and removes the clip). Per the review-cost rule they were excluded from the first
PR and are recorded as NO_SIGNAL (mathematically redundant clip already optimised
adequately by the compiler).
