# VulnGen

A reproducible framework for evaluating the prevalence of security-relevant
defects in code, using a SAST ensemble with manual adjudication, plus
**SecureBench**, a task-specification schema.

> **Status: framework + protocol for an in-progress empirical study.**
> This repository provides the detection tooling and a study harness. The
> empirical results come from running the harness on a public dataset (see
> [`study/PROTOCOL.md`](study/PROTOCOL.md)); they are not pre-baked into the
> repo. Any numbers under `data/results/` are **illustrative worked examples,
> not measured findings** — see the note in that directory.

---

## What this repo is, and is not

**It is:** a working, defensive code-security analysis pipeline (a SAST
ensemble with majority voting, CWE normalisation, CVSS banding), the statistics
needed to report results rigorously (Wilson confidence intervals, Cohen's κ,
FPR/recall, effect sizes), and an end-to-end study harness that runs the
pipeline over a public dataset and combines it with hand adjudication to produce
**measured, traceable** results.

**It is not:** a code-generation or exploit-production system. There is no
generation engine, by design (see [`vulngen/generation/README.md`](vulngen/generation/README.md)).
The study analyses code that already exists in a published dataset.

---

## The empirical study (start here)

The honest, runnable study lives in [`study/`](study/). Full protocol in
[`study/PROTOCOL.md`](study/PROTOCOL.md). In short:

```bash
# 1. Download a public dataset (e.g. SecurityEval) into study/data/
# 2. Run the detection ensemble over it
python study/run_study.py analyze --dataset securityeval --path study/data/SecurityEval

# 3. Draw a stratified manual-review subset (your review budget)
python study/run_study.py sample --n 150

# 4. Hand-adjudicate study/adjudication/sheet_blank.csv -> sheet_filled.csv
# 5. Compute verified results (VR + 95% CI, ensemble FPR, recall, Cohen's kappa)
python study/run_study.py finalise --adjudication study/adjudication/sheet_filled.csv
```

`verified_results.json` is your actual result. Report it as measured on the
reviewed subset of the named dataset, and compare to published baselines for
that dataset (see the protocol).

---

## Components

```
vulngen/
  sast/          SAST ensemble: detectors + majority voting (detection only)
  adjudication/  inter-rater agreement (Cohen's kappa), FPR estimation
  metrics/       VR + Wilson CIs, CVSS banding, SUT + sensitivity, effect sizes
  generation/    intentionally STUBBED (no generator / no exploit engine)
  cli.py         analyze + metrics commands
securebench/     task-specification schema + tier definitions
study/           the empirical study harness + PROTOCOL.md  <-- start here
prompts/         defensive prompt-hardening templates
scripts/         helper scripts
tests/           unit tests for the statistics and study math
data/            illustrative worked examples ONLY (clearly labelled)
```

## Install

```bash
git clone <your-repo-url>
cd vulngen
python -m venv .venv && source .venv/bin/activate
pip install -e .
```

Optional external SAST tools strengthen the ensemble (auto-detected if present):
`bandit`, `semgrep`, `cppcheck`. With none installed, built-in pattern detectors
keep the pipeline runnable on a clean machine.

## Run the tests

```bash
PYTHONPATH=. python -m pytest tests/ -q
```

## Scope and honesty

Claim only what you measure. This framework measures what a SAST ensemble plus
human review can confirm on a specific public dataset. It does not by itself
establish in-the-wild rates, multi-model comparisons, or fine-tuning effects —
each of those is a separate study requiring its own data.

## License

Code: MIT (see [`LICENSE`](LICENSE)). SecureBench specifications: CC-BY-4.0.
