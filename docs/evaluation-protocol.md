# Evaluation Protocol

## 1. Purpose

This document defines the evaluation methodology used to assess PAC-CF and its comparison methods.

The objective is to evaluate counterfactual validity, temporal consistency, WFFC feasibility, intervention size, search status, and computational cost.

A high counterfactual success rate alone is insufficient. The final intervention must also satisfy the declared constraints and its reported score must correspond to the exact returned intervention.

## 2. Evaluation principles

The evaluation follows these principles:

1. Freeze the predictor before counterfactual search.
2. Freeze the evaluation population before comparing methods.
3. Use the same target identities when a paired comparison is intended.
4. Apply identical external validation criteria to all methods.
5. Separate native method behavior from post-hoc feasibility filtering.
6. Report failures and unsuccessful targets.
7. Preserve the distinction between exact certification and heuristic success.
8. Do not describe synthetic-data results as real-world financial-crime validation.

## 3. Historical protocol revisions

The evaluation protocol evolved during development.

An earlier protocol used a smaller sample and a threshold of 0.5.

The later evaluation process introduced explicit target manifests, development/evaluation separation, and a fixed threshold of 0.8.

Historical results from different protocol versions must not be combined without explaining their differences.

## 4. Final evaluation population

The documented final evaluation population contains:

- 250 true-positive targets.
- 250 false-positive targets.
- 500 unique target transactions in total.

For this target construction, the initial prediction threshold used to define TP and FP was 0.5.

The later counterfactual decision threshold was fixed at 0.8.

The two thresholds serve different purposes and must not be conflated.

### 4.1 Target definitions

Using the initial target-selection threshold of 0.5:

- TP: model score at least 0.5 and ground-truth label 1.
- FP: model score at least 0.5 and ground-truth label 0.

The target population was selected deterministically.

### 4.2 Development set

The documented development set contains:

- 25 TP targets.
- 25 FP targets.
- 50 targets total.

### 4.3 Evaluation set

The documented evaluation set contains:

- 250 TP targets.
- 250 FP targets.
- 500 targets total.

The development and evaluation populations were checked for overlap.

The recorded evaluation process also checked target-ID shuffle invariance and independently verified TP/FP membership.

## 5. Dataset and manifest identity

Recorded dataset SHA-256:

```text
B19D39F515523373F991B689C07E11E7B0B95C17A2C27A87D91584AE16C5B040
```

Recorded target manifest SHA-256:

```text
07253F252C812232EA1E2FE699442C281EE7EA133B0C8164FAB5FEF815A43918
```

A separate mock manifest was identified as invalid and must not be used as experimental evidence.

A reproduction must verify the dataset and manifest hashes before evaluating results.

## 6. Threshold

The final experimental counterfactual threshold was:

\[
\tau=0.8
\]

This is a fixed experimental decision boundary.

It is not a calibrated probability threshold, a regulatory threshold, or a universal AML risk boundary.

The threshold's empirical justification is not established by the recorded documentation and should be treated as an experimental choice.

## 7. Predictor parity

Before evaluating counterfactual methods:

1. Load the designated checkpoint.
2. Verify checkpoint identity.
3. Verify feature preprocessing.
4. Verify graph-context construction.
5. Evaluate frozen targets.
6. Compare inference outputs against the designated parity evidence.

A checkpoint mismatch or preprocessing mismatch invalidates direct comparison.

The reconstructed A4-equivalent checkpoint must be identified separately from the original historical A4 checkpoint.

## 8. PAC-CF evaluation metrics

### 8.1 Counterfactual success rate

\[
\mathrm{SuccessRate}=
\frac{\text{valid counterfactual successes}}
{\text{eligible targets}}
\]

Already-safe targets should be reported separately rather than silently counted as successful interventions.

### 8.2 WFFC validity

\[
\mathrm{WFFCValidity}=
\frac{\text{WFFC-valid final interventions}}
{\text{interventions evaluated}}
\]

The denominator must be stated.

### 8.3 Temporal validity

The percentage of final interventions that satisfy the documented temporal rules.

### 8.4 Score-mask consistency

The percentage of returned interventions for which the reported final score corresponds to the exact returned final mask.

Execution-path consistency and independent score recomputation should be reported separately when both are available.

### 8.5 Intervention size

Report the number of removed edges.

For successful interventions, useful summaries include:

- Mean.
- Median.
- Standard deviation.
- Interquartile range.
- Minimum.
- Maximum.

Do not mix successful-only intervention sizes with failed-target counts.

### 8.6 Search status

Report the number and percentage of results in each status category:

- `CERTIFIED_GLOBAL_MINIMUM`
- `EXACT_SEARCH_SUCCESS_NO_CERTIFICATE`
- `HEURISTIC_COUNTERFACTUAL`
- `NO_FEASIBLE_COUNTERFACTUAL_FOUND_WITHIN_SEARCH`
- `ALREADY_SAFE`

The bounded nature of certification must be stated.

### 8.7 Runtime

Report:

- Total runtime.
- Per-target runtime.
- Mean and median runtime.
- Hardware.
- Batch size.
- Whether projection time is included.
- Whether preprocessing and model loading are included.

## 9. Historical PAC-CF results

### 9.1 V1

The recorded V1 configuration search included 81 configurations.

The selected configuration was:

- Learning rate: 0.01.
- Sparsity coefficient: 0.05.
- Maximum iterations: 100.
- Restarts: 1.

Historical results:

| Metric | Result |
|---|---:|
| Development success | 29/50 (58%) |
| Evaluation success | 228/500 (45.6%) |
| WFFC validity | 90.6% |
| Temporal validity | 100% |
| Binary-mask validity | 67% |
| Invalid outputs | 272 |

V1 had identified defects involving global RNG sampling, fractional feasibility masks being assigned to binary interventions, and stale score handling.

V1 is a historical development result, not the definitive PAC-CF evaluation.

### 9.2 V2

V2 introduced deterministic per-target seeds and a discrete causal cascade simulator with an independent exhaustive oracle.

The development set recorded 49/50 successes (98%).

The later evaluation was invalidated by a stale-score defect: the final returned mask could differ from the mask whose score had been calculated.

The frozen V2 result must not be treated as a valid definitive evaluation.

### 9.3 V3

The primary V3 result set contains 479 targets.

Recorded results:

| Outcome | Count |
|---|---:|
| Counterfactual successes | 473 |
| No feasible counterfactual found within search | 5 |
| Already safe | 1 |
| Total | 479 |

Among the 473 counterfactual successes:

| Status | Count |
|---|---:|
| `CERTIFIED_GLOBAL_MINIMUM` | 471 |
| `EXACT_SEARCH_SUCCESS_NO_CERTIFICATE` | 23 |

The status counts above sum to 494 and therefore do not reconcile with the 473-success figure.

**This discrepancy must be resolved against the primary experiment artifacts before these status counts are used as a final publication claim.**

A later 500-target artifact recorded:

- 500 unique targets.
- 471 certified results.
- 23 exact-search successes without certification.
- 5 no-feasible results.
- 1 already-safe result.
- 494 counterfactual successes.
- 495 targets with success or already-safe outcomes.

The 500-target artifact also has a documented history of duplicate chronological entries in the raw file. The deduplicated result artifact has recorded SHA-256:

```text
9F875BAFCFA702CE12BF471BE000EFDDAF54F1E3D172EC608AE41F8062B73133
```

These 479- and 500-target records must remain distinct until their relationship and the status-count discrepancy are verified.

## 10. Comparison methods

The documented comparison methods are CoDy and TemGX.

Comparison must distinguish:

- Native method output.
- Post-hoc WFFC filtering.
- Temporal validation.
- Strict independent fidelity validation.
- Runtime.
- Intervention size.

Historical comparison results:

| Method | Targets with generated results | Strict fidelity successes | Strict success rate |
|---|---:|---:|---:|
| PAC-CF | 479 | 471 | 98.33% |
| CoDy | 479 | 273 | 56.99% |
| TemGX | 479 | 104 | 21.71% |

These figures are recorded under the documented strict-filter comparison. They should not be presented as a fully controlled apples-to-apples comparison unless the same target population, predictor, candidate history, and independent validation implementation are confirmed for every method.

Additional historical baseline records:

**CoDy**
- Full 479-target run.
- 290/479 generated successes under its recorded native procedure.
- Mean intervention size: 6.56.
- Independent fidelity: 100% among the 290 recorded successes.
- Post-hoc WFFC validity: 94.1%.
- Temporal validity: 100%.
- Total runtime: approximately 115.26 seconds.

**TemGX**
- Generated interventions for 479/479 targets.
- Independent strict fidelity: 109/479 (22.8%).
- Mean intervention size: 4.68.
- Total runtime: approximately 14.05 seconds.

The strict-filter figures and native baseline figures have different denominators and validation stages. They must not be merged into one table without explicitly explaining the difference.

## 11. Runtime evaluation

A recorded GPU inference benchmark used:

- Python 3.14.7.
- PyTorch 2.14.0+cu132.
- CUDA enabled.
- RTX 3060 Laptop GPU, approximately 6143.5 MB reported memory.
- Reconstructed checkpoint SHA-256 beginning with `1CE6BB6C`.

Recorded candidate-scoring throughput:

| Batch size | Candidates per second |
|---:|---:|
| 1 | 129.10 |
| 2 | 186.16 |
| 4 | 495.43 |
| 8 | 833.55 |
| 16 | 1459.89 |
| 32 | 3239.92 |

Score equivalence testing recorded zero maximum and mean absolute error for the tested batch configurations.

A projected 500-target runtime of approximately 11.6 minutes was recorded, but projection time was not measured in that estimate. It must not be presented as a complete end-to-end runtime measurement.

## 12. Statistical analysis

Where appropriate, report confidence intervals for proportions using Wilson intervals.

For continuous metrics, report median and interquartile range alongside mean and standard deviation.

Paired binary comparisons may use McNemar's test when the same targets are evaluated by both methods under compatible conditions.

Paired continuous comparisons may use Wilcoxon signed-rank testing only when the compared values are genuinely paired and their definitions are comparable.

Do not claim statistical significance without the corresponding test, assumptions, sample size, and result.

## 13. Evaluation limitations

The evaluation is limited by:

- Synthetic data.
- Historical artifact availability.
- The distinction between original and reconstructed checkpoints.
- Different baseline implementation behavior.
- Bounded exact-search space.
- WFFC's partial-observability assumptions.
- Incomplete evidence for some historical claims.
- The unresolved status-count discrepancy described above.

## 14. Publication requirement

Before using these results in a final paper, reconcile all target counts and statuses from the primary machine-readable result artifacts.

The paper must not present conflicting 479- and 500-target summaries as though they were one experiment.

Every table should state its population, denominator, validation stage, and evidence source.
