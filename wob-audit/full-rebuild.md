# Full rebuild

`wob/full-rebuild` (commit `4817d860f`) combines the five READY_FOR_PR candidates on top
of upstream `d03dc3c58`.

## Contents

- rgb2rgba / rgba2rgb 4-pixel shuffle
- l2rgb 32-bit broadcast
- ImageChops dead-clip removal
- 3-band histogram pad-byte removal
- zero-width copy guard

## Validation

- Full test suite: 5197 passed, 405 skipped, 3 xfailed, 0 failed
- UBSan: clean across all differential corpora and boundary stress
- Differential corpora (conversions 1224 cases, chops 324, histogram, l2rgb): 0 mismatches

## Interactions

The candidates touch disjoint code paths (different functions or files), so their gains
are additive with no interference. The combined build is behaviourally identical to
upstream and delivers 1.6x-6.9x on representative workloads.

## Purpose

This branch is a combined validation build. It is **not** intended to be submitted
upstream as a single pull request; each candidate is submitted separately.
