# Reproduction Notes

Output of `python scripts/reproduce_claims.py --data data/results/ --report`,
recomputing the paper's headline statistics from the released summary tables.

**Current status: 13 reproduced, 0 discrepancies, 1 needs the full dataset.**

The two discrepancies originally surfaced by the harness have been resolved by
*correcting the claims* (see `CORRECTIONS.md`), not by altering measurements.

## Reproduced (13)

- **GPT-4o weighted-average VR = 19.8%** and all four per-language VRs
  (Python 18.9%, C/C++ 23.2%, JavaScript 17.4%, Java 14.6%), via a Wilson-score
  interval over per-language sample weights (py=3550, c=3400, js=2200,
  java=2250).
- **SUT α-sensitivity (corrected):** full-ranking stability holds for
  α ∈ [0.4, 1.39]; combined hardening stays top-ranked through α = 1.67. Both
  boundaries reproduce exactly.
- **Cohen's d bands (Table VII):** combined = large (1.14), CB = medium (0.71),
  SP = small (0.42), SAFECODER = large (1.38).
- **FPR-corrected aggregate VR** (0.352 × (1 − 0.083) = 0.323) falls inside the
  in-the-wild range [0.317, 0.331].
- **κ = 0.81 label (corrected):** "almost perfect" per Landis & Koch.

## Needs full dataset (1)

- **34.2% High/Critical severity share.** Requires the per-finding severity
  counts; not derivable from the top-CWE summary table. Compute with
  `vulngen metrics` on a full `findings.json`. See `CORRECTIONS.md` for what
  this means for the manuscript.

## How to regenerate

```bash
PYTHONPATH=. python scripts/reproduce_claims.py --data data/results/ --report
PYTHONPATH=. python -m pytest tests/ -q
```
