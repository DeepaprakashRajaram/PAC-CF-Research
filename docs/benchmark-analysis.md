# PAC-CF Benchmark Analysis

## 1. Purpose

This document records the benchmark methodology and reported comparative results for Projection-Aware Certified Counterfactual Search (PAC-CF).

The primary research question is:

**Can PAC-CF generate temporally and financially constrained counterfactual explanations for a frozen temporal graph neural network while maintaining exact score-to-intervention consistency and providing bounded minimum-cardinality certificates?**

The evaluation compares PAC-CF with CoDy and TemGX.

The results must be interpreted in light of differences in native algorithms, feasibility handling, implementation details, and evaluation procedures.

This document distinguishes reported measurements from conclusions that the measurements do not establish.

## 2. Benchmark objectives

The evaluation considers six dimensions.

1. **Counterfactual success:** Whether a method produces an intervention satisfying the configured target-model decision condition.
2. **Feasibility:** Whether the intervention satisfies the declared temporal and WFFC constraints.
3. **Fidelity:** Whether the returned intervention actually changes the frozen target model's decision.
4. **Intervention size:** The number of historical transactions removed.
5. **Certification:** Whether minimum cardinality is established within an exhaustively checked search space.
6. **Runtime:** The computational time required to produce the result.

These dimensions should be reported separately. A high counterfactual success rate does not, by itself, establish feasibility, minimality, or real-world explanatory validity.

## 3. Experimental environment

The documented PAC-CF GPU benchmark environment was:

| Component | Configuration |
|---|---|
| GPU | NVIDIA RTX 3060 Laptop GPU |
| GPU memory | Approximately 6 GB |
| Python | 3.14.7 |
| PyTorch | 2.14.0+cu132 |
| CUDA availability | True |
| Target model | Frozen TGAT |
| Checkpoint | Reconstructed A4-equivalent |
| Checkpoint SHA-256 | `1CE6BB6C2D1C3239C88DE862E9D66A2D0A22894993542F27343D17F57CE18FE5` |
| Evaluation manifest SHA-256 | `07253F252C812232EA1E2FE699442C281EE7EA133B0C8164FAB5FEF815A43918` |

The checkpoint is a reconstructed A4-equivalent artifact. It is not the original lost A4 checkpoint.

The evaluation uses IBM AMLSim HI-Small synthetic transaction data.

The benchmark does not establish performance on real bank transaction systems.

## 4. Evaluation population

The main evaluation protocol uses a frozen population of 500 target transactions:

- 250 true-positive targets.
- 250 false-positive targets.
- 500 unique target IDs.

The target population was selected using the configured model decision threshold and dataset labels.

The dataset hash recorded for the evaluation was:

`B19D39F515523373F991B689C07E11E7B0B95C17A2C27A87D91584AE16C5B040`

The frozen manifest hash was:

`07253F252C812232EA1E2FE699442C281EE7EA133B0C8164FAB5FEF815A43918`

The invalid mock manifest identified during the research process must not be used as experimental evidence.

A separate 368-target subset was later used for a professor-facing brief. It is a subset of the authorized evaluation population and must not be confused with the complete 479-target or 500-target result records.

## 5. Threshold and decision rule

The final documented experimental threshold is:

\[
\tau=0.8
\]

A counterfactual is considered successful when:

\[
p_{\mathrm{cf}}<0.8
\]

This is a fixed experimental decision threshold.

It must not be described as a calibrated fraud probability or a universal AML decision boundary.

The initial protocol also contained experiments using a threshold of 0.5. Those historical experiments must remain distinguishable from the final threshold configuration.

## 6. PAC-CF version history

### 6.1 PAC-CF V1

V1 evaluated 81 configurations.

The selected configuration was:

| Parameter | Value |
|---|---:|
| Learning rate | 0.01 |
| Sparsity coefficient | 0.05 |
| Maximum iterations | 100 |
| Restarts | 1 |

Reported results:

| Metric | Result |
|---|---:|
| Development success | 29/50 (58%) |
| Evaluation success | 228/500 (45.6%) |
| WFFC validity | 90.6% |
| Temporal validity | 100% |
| Binary-mask validity | 67% |
| Invalid results | 272 |

Three important implementation defects were identified:

1. Candidate sampling depended on global random-number-generator state.
2. The greedy stage could assign a fractional WFFC feasibility mask to a binary intervention mask.
3. Stale-score logic could allow the reported score to refer to a different intervention from the returned mask.

V1 is retained as historical development evidence. Its results should not be presented as the definitive PAC-CF performance.

### 6.2 PAC-CF V2

V2 introduced:

- Deterministic target-specific random seeds.
- A discrete causal-cascade simulator.
- An independent exhaustive feasibility oracle.

The development set initially reported 49/50 successful results.

However, subsequent evaluation identified a stale-score defect.

The principal issue was that WFFC projection could modify an intervention through a causal cascade after the model score had already been computed.

Because TGAT predictions are not assumed to be monotonic under edge removal, the score could no longer be treated as valid for the projected intervention.

The final evaluation reported:

| Metric | Result |
|---|---:|
| Evaluation success | 23/500 (4.6%) |

V2 is frozen as a historical development version and is not the definitive method.

### 6.3 PAC-CF V3

V3 was designed around four stages:

1. Candidate proposal.
2. WFFC feasibility projection.
3. Exact TGAT evaluation.
4. Bounded discrete search and certification.

Its principal design correction is exact evaluation of the final projected intervention mask.

The documented 479-target primary result record reports:

| Outcome | Count |
|---|---:|
| Successful counterfactuals | 473 |
| No feasible counterfactual found | 5 |
| Already safe | 1 |
| Total | 479 |

The reported success proportion is:

\[
\frac{473}{479}\times100
\approx98.75\%
\]

The corresponding reported status summary contains:

- 471 `CERTIFIED_GLOBAL_MINIMUM`.
- 23 `EXACT_SEARCH_SUCCESS_NO_CERTIFICATE`.

These status counts sum to 494, not 473.

They therefore cannot be assumed to describe the same outcome partition as the 479-target table without additional source-level reconciliation.

The discrepancy is preserved deliberately. It must not be silently corrected or merged into a single result.

## 7. Separate 500-target artifact

A separate deduplicated 500-target artifact reports:

| Outcome/status | Count |
|---|---:|
| Certified minimum status | 471 |
| Exact success without certificate | 23 |
| No feasible result | 5 |
| Already safe | 1 |
| Total | 500 |

The reported counterfactual success count is:

\[
471+23=494
\]

Thus:

\[
\frac{494}{500}\times100=98.8\%
\]

The success-or-already-safe count is:

\[
\frac{495}{500}\times100=99\%
\]

Additional reported checks:

| Check | Result |
|---|---:|
| Unique targets | 500 |
| WFFC validation | 100% |
| Score consistency | 100% |

The score-consistency figure is an execution-path consistency result. It should not be described as an independent proof of model correctness.

The WFFC figure refers to the implemented validator, not to actual bank-ledger verification.

### 7.1 Artifact provenance caveat

The original raw result file contained 517 chronological lines and was overwritten during the result-handling process.

Forensic review identified 17 duplicate chronological entries associated with overlapping execution from process PID 12716 and a resumed task.

The deduplicated 500-target result artifact has SHA-256:

`9F875BAFCFA702CE12BF471BE000EFDDAF54F1E3D172EC608AE41F8062B73133`

The forensic boundary audit classified the primary execution as status A: Cleared.

The 500-target artifact and the 479-target primary record must remain separate until their relationship is established from authoritative raw evidence.

## 8. Baseline methods

### 8.1 CoDy

CoDy is a counterfactual explanation method for dynamic graphs.

Its native approach uses Monte Carlo Tree Search and heuristic guidance.

Reported full 479-target results:

| Metric | Result |
|---|---:|
| Successful outputs | 290/479 |
| Success rate | 60.5% |
| Mean intervention size | 6.56 |
| Independently verified fidelity among successes | 100% |
| WFFC validity after post-processing | 94.1% |
| Temporal validity | 100% |
| Total runtime | 115.26 seconds |
| Mean runtime per target | 0.2287 seconds |
| Median runtime per target | 0.2492 seconds |
| Minimum runtime | 0.0136 seconds |
| Maximum runtime | 0.5044 seconds |

The WFFC figure is a post-processing evaluation. It must not be presented as a native CoDy guarantee.

### 8.2 TemGX

TemGX is a training-free counterfactual explanation method for temporal graph models.

Reported results:

| Metric | Result |
|---|---:|
| Generated interventions | 479/479 |
| Strictly verified fidelity | 109/479 |
| Strict fidelity rate | 22.8% |
| Mean intervention size | 4.68 |
| Total runtime | 14.05 seconds |
| Mean runtime per target | 0.0117 seconds |
| Median runtime per target | 0.0122 seconds |
| Minimum runtime | 0.0071 seconds |
| Maximum runtime | 0.0230 seconds |

The evaluation identified an implementation-specific concern: `genInstanceX` and `compute_prediction_with_subgraph` appeared to evaluate a complement of selected edges in the tested execution path.

Some internal score changes did not reproduce when the exact returned deletion set was independently evaluated.

This is a bounded finding about the tested implementation and execution path. It must not be generalized into a claim that the original TemGX method is universally incorrect.

## 9. Strict comparative figures

A strict comparison record reports:

| Method | Strictly valid results | Rate |
|---|---:|---:|
| PAC-CF V3 | 471/479 | 98.33% |
| CoDy | 273/479 | 56.99% |
| TemGX | 104/479 | 21.71% |

These figures differ from the broader headline success counts because they apply stricter result filters.

They must not be mixed with the 473/479 PAC-CF primary outcome, 494/500 deduplicated artifact, or 109/479 TemGX fidelity count without explicitly identifying the filter and denominator.

The exact filter definitions and source result rows should accompany any publication-ready comparative table.

## 10. Runtime benchmark

The GPU inference benchmark measured candidate-scoring throughput under different batch sizes.

| Batch size | Throughput (candidates/second) |
|---:|---:|
| 1 | 129.10 |
| 2 | 186.16 |
| 4 | 495.43 |
| 8 | 833.55 |
| 16 | 1,459.89 |
| 32 | 3,239.92 |

The reported maximum absolute and mean absolute score differences between the tested batch configurations and reference scoring were both zero.

This supports score equivalence for the tested benchmark inputs and execution configuration.

A projected runtime of approximately 11.6 minutes for 500 targets was reported.

This is an estimate, not a complete measured end-to-end runtime. It does not include a separately measured cost for every projection, validation, serialization, or orchestration operation.

## 11. Predictor-aware localization benchmark

A 10-target localization experiment compared local graph evaluation with global graph evaluation.

Reported local graph sizes ranged from 3 to 30 edges.

The global graph contained approximately 0.68 million to 4.6 million edges.

Reported score differences:

| Statistic | Absolute difference |
|---|---:|
| Maximum | 3.209 × 10⁻⁴ |
| Mean | 1.263 × 10⁻⁴ |
| Median | 7.981 × 10⁻⁵ |
| 95th percentile | 3.162 × 10⁻⁴ |

These results are preliminary and limited to 10 targets.

They do not establish exact equivalence between local and global inference, zero approximation error, or constant-time complexity.

## 12. Statistical reporting

A publication-ready evaluation should include uncertainty and paired comparisons where appropriate.

Recommended reporting:

- Wilson 95% confidence intervals for proportions.
- Median and interquartile range for skewed runtime and intervention-size distributions.
- Paired McNemar tests for comparable binary outcomes.
- Wilcoxon signed-rank tests only for genuinely comparable paired continuous measurements.
- Successful-only intervention-size statistics, clearly labelled.
- Per-target result records to support independent reproduction.

Statistical tests must use genuinely matched target populations and equivalent outcome definitions.

No statistical significance claim should be made solely from the descriptive aggregate figures in this document.

## 13. Interpretation

The reported V3 results suggest that the method can produce a high proportion of valid model-level counterfactuals on the evaluated synthetic AML transaction population.

The strongest architectural contribution is not the headline success percentage alone.

It is the separation of:

1. Proposal generation.
2. Feasibility projection.
3. Exact final-mask scoring.
4. Bounded discrete search.
5. Independent validation.
6. Explicit certification status.

This separation addresses a central failure mode observed in earlier versions: reporting a score for an intervention that was subsequently changed by feasibility projection.

However, the evidence does not establish that PAC-CF explanations reveal real-world transaction causality, actual account balances, criminal intent, or confirmed fraud.

## 14. Limitations and unresolved evidence

The following issues must be resolved or explicitly disclosed before final publication.

1. Reconcile the 479-target primary result counts with the 479-target certification-status counts.
2. Establish the exact relationship between the 479-target primary record and the separate 500-target artifact.
3. Preserve the distinction between broad success rates and strict comparative filters.
4. Verify the exact denominator and filtering procedure for every baseline metric.
5. Report complete per-target intervention-size distributions.
6. Measure end-to-end runtime, including projection and independent validation.
7. Reproduce localization results on a larger target sample.
8. Verify the mathematical properties of WFFC against the final implementation.
9. Establish the provenance of all source result files and configuration files.
10. Avoid treating synthetic AMLSim results as real-world deployment evidence.

## 15. Conclusion

PAC-CF V3 reports promising results on a synthetic AML temporal graph benchmark.

Its design emphasizes exact evaluation of the final intervention, temporal and WFFC validation, and bounded certification rather than relying solely on proposal-stage scores.

The reported results remain subject to the documented population and artifact discrepancies.

Until those discrepancies are reconciled from authoritative source records, the separate result populations must remain separate and all comparative claims must state their precise denominators and validation criteria.
