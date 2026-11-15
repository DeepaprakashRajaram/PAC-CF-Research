# Reported Experimental Results

## 1. Purpose

This document consolidates the historical experimental results recorded during the development of Projection-Aware Certified Counterfactual Search (PAC-CF).

It distinguishes:

- Original TGAT training results.
- Reconstructed TGAT results.
- PAC-CF V1, V2, and V3 results.
- Baseline comparison results.
- WFFC validation results.
- Runtime benchmarks.

These values are historical research records. Their inclusion does not mean that every original experiment has been independently reproduced.

Where source artifacts are unavailable or reported summaries conflict, the limitation is stated explicitly.

## 2. TGAT model results

### 2.1 Original A4 experiment

The original A4 training report recorded:

| Metric | Reported value |
|---|---:|
| Best validation ROC-AUC | 0.9425262088 |
| Best epoch | 8 |
| Early-stop epoch | 13 |
| PR-AUC at best ROC-AUC | 0.7627253 |
| Legacy baseline ROC-AUC | 0.9410931 |
| Difference over legacy baseline | +0.0014331 |

The original architecture used:

- Node input dimension: 16.
- Edge feature dimension: 39.
- Hidden dimension: 64.
- Time dimension: 64.
- Attention heads: 4.

**Evidence status:** Historical training report.

The original final A4 checkpoint is unavailable. These metrics must not be presented as results independently reproduced from that original checkpoint.

### 2.2 Original A5 experiment

The original A5 report recorded:

| Metric | Reported value |
|---|---:|
| Seed | 43 |
| Best epoch | 4 |
| Validation ROC-AUC | 0.9373508 |
| PR-AUC | 0.7468530 |
| Early-stop epoch | 6 |

**Evidence status:** Historical training report.

The original final A5 checkpoint is unavailable.

### 2.3 Reconstructed A4-equivalent experiment

A later reconstruction was trained from scratch to continue the research.

Checkpoint SHA-256:

```text
1CE6BB6C2D1C3239C88DE862E9D66A2D0A22894993542F27343D17F57CE18FE5
```

Recorded configuration:

| Parameter | Value |
|---|---|
| Seed | 42 |
| Optimizer | Adam |
| Learning rate | 0.001 |
| Batch size | 1024 |
| Maximum epochs | 50 |
| Early-stopping patience | 5 |
| Dataset | HI-Small cleaned transaction data |
| Split | Validation |
| Hardware | RTX 3060 Laptop GPU |

This checkpoint is a reconstruction, not the original A4 checkpoint.

### 2.4 Reconstructed multi-seed results

| Seed | Best epoch | ROC-AUC | PR-AUC | Stop epoch |
|---:|---:|---:|---:|---:|
| 42 | 4 | 0.9422524 | 0.7415535 | 9 |
| 43 | 4 | 0.9439040 | 0.7709527 | 9 |
| 44 | 3 | 0.9414502 | 0.7517185 | 8 |
| 45 | 9 | 0.9430912 | 0.7939649 | 14 |

Recorded aggregate metrics:

| Metric | Mean | Standard deviation |
|---|---:|---:|
| ROC-AUC | 0.9426744 | 0.0010587 |
| PR-AUC | 0.7645474 | 0.0230920 |

These results describe the reconstructed multi-seed experiments only.

## 3. WFFC validation results

WFFC (Weak Factual Flow Consistency) was tested using deterministic synthetic transaction cases.

The documented validation work included an independent exhaustive pure-Python oracle.

The recorded test areas included:

- Factual-mask identity.
- Maximum-deficit initialization.
- Causal starvation.
- Multi-hop cascade propagation.
- Chronological interleaving.
- Deterministic event ordering.
- Projection idempotence.
- Monotonicity checks.
- Gradient behavior for relaxed masks.
- Agreement between production projection and oracle cases.

The original WFFC synthetic test suite recorded 14 passing tests.

A later PAC-CF invariant suite recorded 20 synthetic invariants.

**Interpretation:** These results support implementation behavior for the tested cases. They do not prove that WFFC reconstructs real account balances or models every possible financial transaction.

## 4. PAC-CF V1

### 4.1 Configuration search

The V1 development process evaluated 81 configurations.

The selected configuration was:

| Parameter | Value |
|---|---:|
| Learning rate | 0.01 |
| Sparsity coefficient | 0.05 |
| Maximum iterations | 100 |
| Restarts | 1 |

### 4.2 Reported results

| Metric | Result |
|---|---:|
| Development successes | 29/50 |
| Development success rate | 58.0% |
| Evaluation successes | 228/500 |
| Evaluation success rate | 45.6% |
| WFFC validity | 90.6% |
| Temporal validity | 100% |
| Binary-mask validity | 67% |
| Invalid outputs | 272 |

### 4.3 Identified defects

Subsequent analysis identified:

1. Global random-number-generator state affecting candidate sampling.
2. Fractional WFFC feasibility masks being assigned to binary intervention masks.
3. Incorrect score handling around the final intervention.

V1 is retained as a historical development stage.

Its results should not be treated as the definitive performance of PAC-CF.

## 5. PAC-CF V2

V2 introduced:

- Deterministic per-target seeds.
- Avoidance of global RNG mutation.
- A discrete causal cascade simulator.
- Independent exhaustive-oracle validation.

### 5.1 Development results

| Metric | Result |
|---|---:|
| Development successes | 49/50 |
| Development success rate | 98.0% |

### 5.2 Evaluation defect

A later evaluation identified a stale-score defect.

The projected final intervention mask could differ from the mask used to calculate the reported model score.

This violated the required score-mask correspondence invariant:

\[
s_{\mathrm{reported}}
=
\sigma(f_\theta(G(M_{\mathrm{final}}),e_q))
\]

V2 was frozen as an invalidated evaluation version.

Its reported evaluation results must not be used as definitive evidence of PAC-CF performance.

## 6. PAC-CF V3

### 6.1 Method

V3 introduced the following pipeline:

**Proposal → Feasibility Projection → Exact TGAT Evaluation → Certified/Bounded Search**

The design included:

- Projection before final scoring.
- Exact final-mask evaluation.
- Mask-hash score caching.
- Deduplication of projected interventions.
- Exact discrete search for small cardinalities.
- Bounded heuristic fallback.
- Independent WFFC validation.
- Explicit result statuses.

### 6.2 Primary 479-target record

The recorded primary V3 summary reports:

| Outcome | Count |
|---|---:|
| Counterfactual successes | 473 |
| No feasible counterfactual found within search | 5 |
| Already safe | 1 |
| Total targets | 479 |

The reported counterfactual success rate is:

\[
\frac{473}{479}\times100
\approx98.75\%
\]

### 6.3 Status-count discrepancy

A separate status summary associated with the V3 results reports:

| Status | Count |
|---|---:|
| `CERTIFIED_GLOBAL_MINIMUM` | 471 |
| `EXACT_SEARCH_SUCCESS_NO_CERTIFICATE` | 23 |
| Total | 494 |

These status counts total 494, not 473.

They cannot all describe the same mutually exclusive categories within the 473-success record.

This discrepancy is unresolved.

The status breakdown must not be combined with the 479-target success summary as though the counts were reconciled.

### 6.4 Separate 500-target artifact

A later artifact records:

| Outcome | Count |
|---|---:|
| Unique targets | 500 |
| Certified results | 471 |
| Exact-search successes without certificate | 23 |
| No feasible counterfactual found within search | 5 |
| Already safe | 1 |
| Counterfactual successes | 494 |
| Success or already-safe outcomes | 495 |

The corresponding reported rates are:

\[
\mathrm{CounterfactualSuccessRate}
=
\frac{494}{500}\times100
=
98.8\%
\]

\[
\mathrm{SuccessOrAlreadySafeRate}
=
\frac{495}{500}\times100
=
99.0\%
\]

These are results from a separate 500-target artifact. They must not be substituted for the 479-target record without explaining the population and artifact differences.

### 6.5 Raw artifact duplication

The original raw result file contained 517 chronological lines due to duplicate entries associated with overlapping processes.

A deduplicated artifact was generated.

Recorded deduplicated artifact SHA-256:

```text
9F875BAFCFA702CE12BF471BE000EFDDAF54F1E3D172EC608AE41F8062B73133
```

The deduplicated artifact and raw chronological file are distinct evidence objects.

The deduplication procedure and per-target records should be retained for independent auditing.

## 7. PAC-CF result interpretation

The V3 statuses express different levels of search evidence.

### `CERTIFIED_GLOBAL_MINIMUM`

A valid minimum-cardinality intervention was found after exhaustive checking of all feasible smaller-cardinality interventions within the declared search space.

The name does not establish unrestricted global optimality over all possible graph interventions.

### `EXACT_SEARCH_SUCCESS_NO_CERTIFICATE`

Exact search found a valid intervention, but the requirements for a minimum-cardinality certificate were not established.

### `HEURISTIC_COUNTERFACTUAL`

A bounded heuristic procedure found a valid intervention without an exact minimum-cardinality certificate.

### `NO_FEASIBLE_COUNTERFACTUAL_FOUND_WITHIN_SEARCH`

No valid intervention was found within the configured search procedure.

This does not prove that no valid intervention exists outside the search space.

### `ALREADY_SAFE`

The target already satisfied the safe-side decision condition under the configured threshold.

## 8. Baseline comparison

The research evaluated CoDy and TemGX as comparison methods.

Their native algorithms and post-hoc validation stages must be distinguished.

### 8.1 CoDy

Recorded full-run results:

| Metric | Result |
|---|---:|
| Targets | 479 |
| Generated successes | 290 |
| Generated success rate | 60.5% |
| Mean intervention size | 6.56 |
| Independent fidelity among recorded successes | 100% |
| Post-hoc WFFC validity | 94.1% |
| Temporal validity | 100% |
| Total runtime | 115.26 seconds |

The mean runtime per target, calculated from the total runtime, is approximately:

\[
\frac{115.26}{479}
\approx0.2406\text{ seconds}
\]

### 8.2 TemGX

Recorded full-run results:

| Metric | Result |
|---|---:|
| Targets | 479 |
| Generated interventions | 479 |
| Strict independent fidelity successes | 109 |
| Strict independent fidelity rate | 22.8% |
| Mean intervention size | 4.68 |
| Total runtime | 14.05 seconds |

The mean runtime per target, calculated from the total runtime, is approximately:

\[
\frac{14.05}{479}
\approx0.0293\text{ seconds}
\]

The documented implementation audit identified behavior in which an internal prediction path appeared to evaluate a complement of selected edges rather than the exact returned deletion set.

This is an implementation-specific finding, not a universal statement about the TemGX method.

## 9. Strict comparison figures

A separate strict-filter comparison recorded:

| Method | Strict successes | Target population | Strict success rate |
|---|---:|---:|---:|
| PAC-CF | 471 | 479 | 98.33% |
| CoDy | 273 | 479 | 56.99% |
| TemGX | 104 | 479 | 21.71% |

These figures are distinct from the native baseline success counts.

They must be interpreted according to the strict filtering and validation procedures used for that comparison.

A definitive comparative claim requires verification that all methods used:

- The same target identities.
- The same frozen predictor.
- The same candidate histories.
- The same threshold.
- The same independent fidelity validator.
- The same temporal validation criteria.
- Compatible runtime measurement boundaries.

Do not infer statistical significance from these percentages alone.

## 10. Runtime benchmark

A separate GPU inference benchmark recorded the following candidate-scoring throughput:

| Batch size | Candidates per second |
|---:|---:|
| 1 | 129.10 |
| 2 | 186.16 |
| 4 | 495.43 |
| 8 | 833.55 |
| 16 | 1459.89 |
| 32 | 3239.92 |

Recorded environment:

- Python 3.14.7.
- PyTorch 2.14.0+cu132.
- CUDA enabled.
- NVIDIA RTX 3060 Laptop GPU.
- Approximately 6143.5 MB reported GPU memory.

The tested batch configurations recorded zero maximum and mean absolute score error.

A projected runtime of approximately 11.6 minutes for 500 targets was recorded.

However, projection time was not measured in that estimate.

Therefore, the estimate must not be described as a complete end-to-end PAC-CF runtime.

## 11. Predictor-aware localization benchmark

A 10-target investigation compared local graph contexts with much larger global graph contexts.

Recorded observations:

| Metric | Result |
|---|---:|
| Local graph size | 3–30 edges |
| Global graph size | Approximately 0.68–4.6 million edges |
| Maximum observed score difference | 0.0003209 |
| Mean observed score difference | 0.0001263 |
| Median observed score difference | 0.00007981 |
| 95th percentile observed difference | 0.0003162 |

This is a 10-target result.

It does not establish universal equivalence between local and global graph inference or zero approximation error.

## 12. Statistical reporting requirements

Future reporting should include:

- Wilson confidence intervals for proportions.
- Median and interquartile range for intervention sizes.
- Mean and standard deviation where appropriate.
- Per-target results.
- Clear denominators.
- Explicit treatment of already-safe targets.
- Paired statistical tests only when the comparison is genuinely paired.

Successful-only intervention-size statistics must not be confused with metrics calculated over every target.

## 13. Evidence limitations

The reported results are subject to the following limitations:

1. The original final A4/A5 checkpoints are unavailable.
2. The reconstructed checkpoint is not identical to the original checkpoint.
3. V1 and V2 contain documented methodological defects.
4. V3 has separate 479-target and 500-target records.
5. The 479-target status breakdown is inconsistent with its reported success count.
6. The 500-target raw artifact contains duplicate chronological entries.
7. Baseline comparisons require common-protocol verification.
8. Runtime estimates do not necessarily include all pipeline stages.
9. Experiments use synthetic AMLSim data.
10. WFFC is a partial-observability feasibility model, not a real bank ledger.

## 14. Publication rule

The numerical summaries in this document must be checked against the original per-target result artifacts before being used as definitive publication claims.

Where the evidence is incomplete or contradictory, the uncertainty must remain visible.

No result should be upgraded from historical to independently reproduced without a new execution and preserved validation evidence.
