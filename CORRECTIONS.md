# Manuscript Corrections

Discrepancies found by `scripts/reproduce_claims.py` and how to correct the
paper. After applying these, the harness reports **0 discrepancies**. The
underlying measurements are unchanged — these are corrections to *claims about*
the measurements.

---

## Correction 1 — SUT α-sensitivity (Section IV-C)

**Current text (incorrect):**
> Rankings across all prompt hardening strategies are fully stable for
> α ∈ [0.5, 2.0] (Spearman ρ = 1.0), confirming that the equal-weighting choice
> does not materially affect conclusions.

**Problem:** With the released VR / functional-pass-rate values, the full
strategy ranking is **not** stable across the whole interval. The functional
pass rates are tightly clustered (≈0.89–0.92) while VR varies widely, so as α
upweights utility the lowest-pass-rate strategy (combined hardening) is demoted.
A fine α-sweep shows:

- Full ranking ρ = 1.0 holds for **α ∈ [0.4, 1.39]**.
- Combined hardening remains the **top-ranked** strategy for **α ∈ [0.4, 1.67]**.
- Beyond α ≈ 1.67 the ranking reorders (at α = 2.0, ρ = −0.143).

**Suggested replacement text:**
> The full strategy ranking is stable for α ∈ [0.4, 1.39] (Spearman ρ = 1.0),
> and the recommended strategy — combined hardening — remains top-ranked up to
> α = 1.67. Because the conclusion (combined hardening is the best zero-cost
> intervention) holds across the entire range in which utility is not weighted
> more than ~1.7× security, the equal-weighting choice (α = 1.0) does not affect
> the paper's conclusions. We report the stability boundary explicitly rather
> than claiming unconditional invariance.

This is a *stronger* paper position: it states a precise robustness boundary
instead of an overbroad invariance claim a reviewer could falsify in one line.

---

## Correction 2 — Cohen's κ label (Section IV-E)

**Current text (incorrect label):**
> Expert review of 1,600 stratified samples yields κ = 0.81 (substantial
> agreement) …

**Problem:** By the canonical Landis & Koch bands, κ ≥ 0.80 is **"almost
perfect"**; "substantial" is the 0.60–0.80 band. The value 0.81 is correct; only
the adjective is wrong.

**Suggested replacement text:**
> … yields κ = 0.81 (almost perfect agreement, per Landis & Koch) …

(Apply the same fix anywhere else κ = 0.81 is described as "substantial,"
including the Abstract if present.)

---

## Open item — High/Critical severity share (Section, Abstract: "34.2%")

**Status:** Cannot be verified from the released summary tables. The 34.2% figure
is the share of *all confirmed weaknesses* at High or Critical CVSS. The top-CWE
table (Table VI) only gives a representative score per CWE and a prevalence mix;
it does not contain the per-finding severity distribution needed to recompute
34.2%.

**What's required:** the full per-finding dataset (each confirmed weakness with
its CVSS base score). With that file, `vulngen metrics --findings findings.json`
computes the High/Critical share directly and the harness can verify it. Until
that data is provided, the 34.2% claim is unverified by this artifact — it should
either be backed by the released findings file or, if that file does not exist,
the claim should be removed or scoped to what was actually measured.

This is the item that most affects whether the paper reads as "well-researched."
A reviewer will expect the raw findings behind every aggregate statistic.
