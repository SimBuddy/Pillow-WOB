# WOB Audit — Pillow

A systematic repository-scale performance audit of the Pillow imaging library.

## What was done

A whole-repository performance audit using an evidence-driven, non-learning
optimisation architecture. The goal was to find structurally exploitable headroom in
Pillow's image-processing code, validate every candidate for exact behaviour, and
publish each as an independent, reviewable contribution.

## Method

- whole-repository map of Python and C layers
- top-down and bottom-up scan of image-processing paths
- regime classification (compute-bound, memory-bound, compiler-vectorisation, etc.)
- prove-headroom-first (explain where the time goes before changing code)
- candidate portfolio with direct upstream-vs-candidate comparison
- differential correctness (byte-exact output comparison over deterministic corpora)
- benchmark gates with a measured noise floor
- negative-result preservation
- combined-build validation
- terminal "nitpicker" simplification of frozen behaviour

## Scope

- Baseline: Pillow `main` at commit `d03dc3c587a9b0bdc9e4304d70d59a635a87b742`
  (13.0.0.dev0), 2026.
- Measured on ARM64 (aarch64) Linux. Candidate branches are validated by the full
  Pillow test suite and UBSan.

## Results

- Targets surveyed: 18
- Experiments/probes run: ~10 across the whole campaign
- READY_FOR_PR: 5
- REJECTED: 6
- NO_SIGNAL: 6
- Correctness findings: 1 (plus 1 false-positive corrected)

## Strongest findings

| target | mechanism | speedup (1080p) | status | branch |
|---|---|---|---|---|
| RGB <-> RGBA conversions | 4-pixel 32-bit shuffle | ~6.7x | READY_FOR_PR | wob/rgb2rgba-performance |
| L -> RGB/RGBA conversions | 32-bit byte broadcast | ~4.7x | READY_FOR_PR | candidate/converter |
| RGB/YCbCr/LAB/HSV histogram | drop discarded pad byte | ~3.3x | READY_FOR_PR | candidate/histogram |
| ImageChops.difference | drop provably-dead clip | ~1.8x | READY_FOR_PR | candidate/imagechops-difference |
| Image.copy() zero-width | guard memcpy(NULL,0) | correctness | READY_FOR_PR | candidate/copy-zero-width |

## Full rebuild

`wob/full-rebuild` combines all five candidates. It passes the full test suite
(5197 passed) and UBSan, and the gains are additive (1.6x-6.9x across representative
workloads). See `wob-audit/full-rebuild.md`.

## Candidate contributions

- [RGB <-> RGBA](wob-audit/candidates/rgb-rgba.md)
- [L mode conversions](wob-audit/candidates/converters.md)
- [Histogram](wob-audit/candidates/histogram.md)
- [ImageChops](wob-audit/candidates/imagechops.md)
- [Copy zero-width](wob-audit/candidates/copy-zero-width.md)

## Negative knowledge

See [negative results](wob-audit/negative-results.md).

## Disclaimer

Candidate branches are independent engineering proposals. The `wob/full-rebuild`
branch is a combined validation build and is not intended to be submitted upstream as a
single pull request.
