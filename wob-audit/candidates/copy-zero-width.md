# Candidate — Copy zero-width images

    TARGET=Image.copy() on zero-width images
    STATUS=CORRECTNESS_FINDING + READY_FOR_PR
    FILES=src/libImaging/Copy.c
    MECHANISM=guard the copy loop with linesize > 0
    HEADROOM=n/a (correctness)
    CHANGE=skip memcpy when the linesize is zero
    CORRECTNESS=zero-width copy + same-mode convert stress; full suite; UBSan
    PERFORMANCE=n/a
    PORTABILITY=pure C
    MAINTENANCE_COST=trivial
    INTERACTIONS=independent
    BRANCH=candidate/copy-zero-width
    COMMIT=c8d956119
    PROPOSED PR TITLE=Avoid copying zero-sized image buffers

---

`ImagingCopy` copies the source to the destination with `memcpy`. For a zero-width
image the row pointers are `NULL`, so copying a zero-width image passed `NULL` to
`memcpy`, which is undefined behaviour. The change skips the copy when the linesize is
zero. Reachable via `Image.new("RGB", (0, 5)).copy()`.
