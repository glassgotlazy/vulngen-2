# VulnGen

A multi-stage security-assessment pipeline for evaluating the prevalence of
security-relevant defects in LLM-generated code, plus **SecureBench**, the
task suite used to drive the evaluation.

This repository is the research artifact accompanying:

> *VulnGen: A Large-Scale Multi-Lingual Analysis of Security Vulnerabilities in
> LLM-Generated Code.*

It is intended to let reviewers **reproduce and verify the paper's claims** from
result data, and to re-run the **defensive** analysis stages on a corpus of code
samples.

---

## Scope and safety boundary

VulnGen has two conceptual halves:

| Half | What it does | Status in this repo |
|------|--------------|---------------------|
| **Detection / analysis** (Stages 4–6) | Runs a SAST ensemble, adjudicates findings, normalises CWEs, scores CVSS, and computes all reported statistics. | **Fully implemented and runnable.** |
| **Generation** (Stages 1–3) | Curates tasks and prompts LLMs to produce code samples. | **Stubbed by design.** See [`vulngen/generation/README.md`](vulngen/generation/README.md). |

The generation stage is deliberately left as an interface with no working
exploit-producing implementation. The reusable, verifiable scientific
contribution — the detection ensemble and the statistics that back every table
and figure — runs end-to-end on sample/result data without it. To reproduce the
paper you supply (a) the released result tables, or (b) your own corpus of code
samples to analyse; you do not need the generation engine.

---

## Pipeline (six stages)

```
Stage 1  Task Curation        SecureBench tiers S / I / C        [schema only]
Stage 2  Prompt Construction  hardening conditions               [templates incl.]
Stage 3  Code Generation      LLM sampling                       [stub interface]
Stage 4  Detection Ensemble   SAST + dynamic + expert adjudication[implemented]
Stage 5  Real-World Validation in-the-wild snippet analysis       [implemented]
Stage 6  Normalisation/Scoring CWE map + CVSS v3.1 banding        [implemented]
```

## Install

```bash
git clone https://github.com/amity-cs/vulngen
cd vulngen
python -m venv .venv && source .venv/bin/activate
pip install -e .
```

Optional external SAST tools (the ensemble auto-detects whichever are present and
records which contributed): `bandit`, `semgrep`, `cppcheck`, CodeQL CLI,
SpotBugs/FindSecBugs. With none installed, the ensemble still runs using the
built-in lightweight pattern detectors so the pipeline is reproducible on a clean
machine.

## Reproduce the paper's claims

```bash
# Recompute every headline statistic from the released result tables
python scripts/reproduce_claims.py --data data/results/ --report

# Run the detection ensemble on your own code corpus
python -m vulngen.cli analyze --input path/to/samples/ --out findings.json

# Compute VR, CIs, CVSS bands, SUT + sensitivity, effect sizes from findings
python -m vulngen.cli metrics --findings findings.json --out metrics.json
```

`reproduce_claims.py` recomputes, and checks against the paper's stated values:
the 19.8% GPT-4o VR and its 95% CI, the 35.2% aggregate VR, the 34.2%
High/Critical severity share, the SUT α-sensitivity (Spearman ρ = 1.0), the
Cohen's *d* effect sizes (e.g. combined hardening d = 1.14), and the SAST-ensemble
FPR (8.3%) and κ (0.81).

## Layout

```
vulngen/
  ingest/        load + normalise code samples and result tables
  sast/          ensemble runner, tool adapters, majority voting
  adjudication/  inter-rater agreement (Cohen's κ), FPR estimation
  metrics/       VR + CIs, CVSS banding, SUT + sensitivity, effect sizes
  generation/    STUBBED generation interface (no exploit engine)
  cli.py         command-line entry point
securebench/     task schema + tier definitions (specifications, not payloads)
prompts/         verbatim hardening templates from Appendix A (defensive)
scripts/         reproduce_claims.py and helpers
tests/           unit tests for the statistics (verify the math, not the model)
data/            example findings + result tables for a dry run
```

## Citing

See [`CITATION.cff`](CITATION.cff). Artifact DOI: `10.5281/zenodo.vulngen2024`.

## License

Code released under the MIT License (see [`LICENSE`](LICENSE)). SecureBench task
specifications are released under CC-BY-4.0.
