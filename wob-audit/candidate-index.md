# Candidate index

Ranked by review simplicity, confidence, real-world usefulness and maintenance cost —
not by the largest benchmark number.

1. **ImageChops.difference** — `candidate/imagechops-difference` (~1.8x). Smallest,
   clearest change; mathematically self-evident; conventional review surface.
   (Lighter/darker were examined but excluded — no measurable speedup.)
2. **Copy zero-width** — `candidate/copy-zero-width` (correctness). One-line guard,
   unambiguous, no performance claim to defend.
3. **RGB <-> RGBA** — `wob/rgb2rgba-performance` (~6.7x). Largest win; more code and an
   endianness discussion, so reviewed after the simpler shuffle establishes the pattern.
4. **L mode conversions** — `candidate/converter` (~4.7x). Same broadcast pattern.
5. **Histogram** — `candidate/histogram` (~3.3x). Slightly more behavioural framing.

Each candidate is independent and self-contained.
