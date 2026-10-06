# Methodology

## Whole-repository mapping

Pillow is a mix of Python orchestration (`src/PIL/`) and C kernels (`src/libImaging/`,
compiled into `_imaging`). The audit mapped both: mode conversions, channel operations,
histogram/statistics, point operations, filters, compositing, and the Python-level
orchestration that calls them.

## Regime classification

Each target was classified as, e.g., memory-bandwidth-bound, compute-bound,
branch-heavy, compiler-vectorisation-limited, or Python-boundary-dominated. The
classification selected the optimisation mechanism: compiler-friendly restructuring
applied only where the code was a scalar byte shuffle/broadcast that a compiler could
vectorise.

## Prove-headroom-first

Before changing code, the audit established why headroom existed — bytes conceptually
touched vs. a measured `memcpy` bandwidth ceiling, number of passes, or a serial
dependency. Targets without a credible headroom argument were dropped early.

## Candidate portfolio and comparison

Every candidate was compared directly against the frozen upstream baseline, never
against another candidate. A measured noise floor (pinned prime core, coefficient of
variation ~0.1%) defined what counts as a signal (>=3%) versus noise (<1.5%).

## Differential correctness

Byte-exact output was required. Deterministic corpora (hundreds of cases across sizes,
modes and content) compared upstream and candidate output bit-for-bit. The full Pillow
test suite and UBSan were the final gates.

## Negative-result preservation

Rejected directions were recorded with the experiment, the result, and the reason, so
they are not silently re-attempted.

## Terminal nitpicker

After a candidate's behaviour and performance were frozen, a final simplification pass
removed duplication and dead machinery without changing behaviour.

## Measurement-environment verification

The compute host's actual state (CPU frequency caps, screen/Doze state) was verified;
two earlier measurement artefacts (a screen-off frequency cap inflating speedups, and a
false-positive "out-of-bounds write") were corrected before publication.
