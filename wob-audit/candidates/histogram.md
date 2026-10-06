# Candidate — Histogram for three-channel images

    TARGET=ImagingGetHistogram
    STATUS=READY_FOR_PR
    FILES=src/libImaging/Histo.c
    MECHANISM=drop discarded pad-byte count
    HEADROOM=~3.3x (RGB histogram)
    CHANGE=split the packed loop by band count; 3-band skips the padding byte
    CORRECTNESS=histogram/entropy/equalize/autocontrast differential (0 mismatches),
                test_image_histogram, full suite, UBSan
    PERFORMANCE=2.9x/3.3x/3.3x (256/1080p/4K); L/RGBA unaffected
    PORTABILITY=pure C
    MAINTENANCE_COST=low
    INTERACTIONS=complementary (equalize/autocontrast benefit)
    BRANCH=candidate/histogram
    COMMIT=2c9646f6b
    PROPOSED PR TITLE=Speed up histograms for three-channel images

---

`ImagingGetHistogram` counted the padding byte of three-channel images (RGB, YCbCr, LAB,
HSV), but that byte is never part of the returned histogram. Counting it created a
serial dependency on a single bin. The change stops counting it, leaving the output
unchanged and making the histogram roughly 3x faster for those modes.
