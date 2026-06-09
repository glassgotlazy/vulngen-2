# Project Status

## Where this repo stands

This is a **framework and study harness**, not a completed empirical paper. The
detection pipeline, statistics, and study workflow are implemented and tested.
The empirical results are produced by running the study harness on a public
dataset — they are not pre-included.

- `scripts/selftest_stats.py` verifies the **statistics code is correct** using
  synthetic placeholder inputs (`data/examples/illustrative_tables.json`). It is
  NOT a reproduction of empirical findings.
- `study/run_study.py` produces **real, measured results** when run on a public
  dataset with manual adjudication. See `study/PROTOCOL.md`.

## History note (transparency)

An earlier draft of the accompanying paper described a large-scale multi-model
study (tens of thousands of generated samples, in-the-wild validation,
fine-tuning comparison) for which no underlying measured dataset exists. Those
numbers have been removed from the repo framing and relabelled as illustrative
placeholders. They must not be cited as findings. The supported path forward is
the dataset-based study in `study/`.

## Two claim-labelling fixes worth keeping

If you reuse any of the methodology text, two statistical-labelling points apply:

1. **SUT α-sensitivity** is bounded, not unconditional: a full-ranking stability
   claim holds only up to α ≈ 1.39; the winning strategy stays on top to α ≈ 1.67.
   State the boundary; don't claim invariance across an open range.
2. **Cohen's κ = 0.81** is "almost perfect" (Landis & Koch), not "substantial."

## Checklist before publishing anything from this repo

- [ ] Run the real study (`study/PROTOCOL.md`) and generate `verified_results.json`.
- [ ] Title/abstract describe only what was measured (one dataset, stated review
      fraction, named tools).
- [ ] Every reported aggregate traces to released raw findings.
- [ ] Compare measured VR to published baselines for the same dataset.
- [ ] Remove or clearly fence any number not produced by an actual run.
- [ ] Add a real DOI to CITATION.cff only after depositing a genuine release.
