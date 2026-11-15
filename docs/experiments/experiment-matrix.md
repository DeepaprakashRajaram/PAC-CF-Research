# PAC-CF Experiment Matrix

## 1. Purpose

This document provides a structured record of the experiments conducted or specified during the development and evaluation of Projection-Aware Certified Counterfactual Search (PAC-CF).

It distinguishes completed experiments, historical development experiments, reported benchmarks, and experiments that remain necessary for stronger scientific validation.

The matrix is intended to support reproducibility and prevent results from different configurations or evaluation populations from being combined incorrectly.

## 2. Experiment classification

Each experiment belongs to one of the following categories:

| Category | Meaning |
|---|---|
| Completed | An experiment with a reported result |
| Historical | A superseded experiment retained for development history |
| Diagnostic | An experiment intended to identify a defect or implementation property |
| Benchmark | A comparison or performance measurement |
| Required | An experiment needed before stronger publication claims |
| Unverified | A result whose complete underlying evidence has not been established |

A reported result is not automatically independently reproduced.

## 3. Environment and artifact identifiers

The principal reconstructed model environment:

| Field | Value |
|---|---|
| Dataset | IBM AMLSim HI-Small |
| Model | TGAT |
| Model status | Reconstructed A4-equivalent |
| Checkpoint SHA-256 | `1CE6BB6C2D1C3239C88DE862E9D66A2D0A22894993542F27343D17F57CE18FE5` |
| Dataset SHA-256 | `B19D39F515523373F991B689C07E11E7B0B95C17A2C27A87D91584AE16C5B040` |
| Evaluation manifest SHA-256 | `07253F252C812232EA1E2FE699442C281EE7EA133B0C8164FAB5FEF815A43918` |
| GPU | NVIDIA RTX 3060 Laptop GPU |
| Python | 3.14.7 |
| PyTorch | 2.14.0+cu132 |
| CUDA | Available |

The reconstructed checkpoint must not be represented as the original A4 checkpoint.

The original A4 and A5 checkpoints were lost. Their historical results remain historical evidence, not proof that the original artifacts are available.

## 4. Model reconstruction experiments

| ID | Experiment | Configuration | Reported outcome | Status |
|---|---|---|---|---|
| M-01 | Original A4 training | Seed 42, original A4 configuration | Validation ROC-AUC 0.9425262; best epoch 8 | Historical |
| M-02 | Original A5 training | Seed 43 | Validation ROC-AUC 0.9373508; best epoch 4 | Historical |
| M-03 | A4-equivalent reconstruction | Seed 42, reconstructed from scratch | Validation ROC-AUC 0.9422524; best epoch 4 | Completed |
| M-04 | Multi-seed reconstruction | Seeds 42–45 | Mean ROC-AUC 0.9426744; SD 0.0010587 | Completed |

### 4.1 Multi-seed results

| Seed | Best epoch | Validation ROC-AUC | Validation PR-AUC | Early-stop epoch |
|---:|---:|---:|---:|---:|
| 42 | 4 | 0.9422524 | 0.7415535 | 9 |
| 43 | 4 | 0.9439040 | 0.7709527 | 9 |
| 44 | 3 | 0.9414502 | 0.7517185 | 8 |
| 45 | 9 | 0.9430912 | 0.7939649 | 14 |

The reported aggregate statistics are:

| Metric | Mean | Standard deviation |
|---|---:|---:|
| ROC-AUC | 0.9426744 | 0.0010587 |
| PR-AUC | 0.7645474 | 0.0230920 |

These are four-seed reconstruction results. They do not establish that the lost original A4/A5 artifacts have been recovered.

## 5. PAC-CF development experiments

| ID | Experiment | Purpose | Reported result | Status |
|---|---|---|---|---|
| P-01 | V1 configuration search | Select optimization parameters | 81 configurations evaluated | Historical |
| P-02 | V1 development evaluation | Evaluate initial optimization design | 29/50 success | Historical |
| P-03 | V1 evaluation population | Evaluate initial implementation | 228/500 success | Historical |
| P-04 | V2 deterministic sampling | Remove global RNG dependence | Target-specific seeds introduced | Completed |
| P-05 | V2 discrete feasibility simulation | Validate causal cascade behavior | Independent exhaustive oracle introduced | Completed |
| P-06 | V2 evaluation | Test score and intervention consistency | 23/500 success after defect discovery | Historical |
| P-07 | V3 final-mask evaluation | Ensure score corresponds to projected mask | Exact final-mask evaluation introduced | Completed |
| P-08 | V3 bounded exact search | Establish bounded minimum-cardinality results | Search depths 1–3 | Completed |
| P-09 | V3 heuristic fallback | Search when bounded exact search does not certify a result | Heuristic status introduced | Completed |

## 6. WFFC experiments

| ID | Experiment | Purpose | Reported result | Status |
|---|---|---|---|---|
| W-01 | Initial zero-balance initialization | Test strict zero initialization | Exposed zero-feasibility behavior | Diagnostic |
| W-02 | Relative causal-flow initialization | Address missing starting balances | Maximum-deficit initialization adopted | Completed |
| W-03 | Synthetic feasibility suite | Validate flow behavior | 14 tests passed | Completed |
| W-04 | Independent exhaustive oracle | Compare production behavior with an independent implementation | Production matched oracle on tested single-edge and chain cases | Completed |
| W-05 | Projection idempotence | Test repeated projection | Included in later invariant testing | Completed |
| W-06 | Projection monotonicity | Test ordered input masks | Included in later invariant testing | Completed |
| W-07 | Temporal starvation | Ensure future inflows do not fund earlier outflows | Included in synthetic validation | Completed |
| W-08 | Interleaved chronology | Validate chronological event handling | Included in synthetic validation | Completed |
| W-09 | Independent final-mask validation | Check final feasibility independently | Reported 100% for the separate 500-target artifact | Completed, artifact-specific |

The synthetic tests support the tested implementation cases. They do not prove that WFFC reconstructs actual bank balances.

## 7. PAC-CF V3 invariant experiments

The V3 validation suite included 20 synthetic invariants.

The documented areas include:

- Exact final-mask score consistency.
- WFFC projection behavior.
- Idempotence.
- Independent feasibility validation.
- Duplicate-candidate handling.
- Deterministic execution.
- Early certification behavior.
- Stale-score regression prevention.

These tests support implementation behavior for the tested scenarios.

The exact test inventory and source-code references should be maintained alongside the test suite for publication-grade auditability.

## 8. Target population experiments

| ID | Experiment | Population | Purpose | Status |
|---|---|---:|---|---|
| D-01 | Initial development protocol | 50 | Early parameter and logic evaluation | Historical |
| D-02 | Frozen evaluation population | 500 | Main evaluation population | Completed |
| D-03 | Primary execution record | 479 | Primary V3 execution record | Completed, reconciliation required |
| D-04 | Professor-facing subset | 368 | Restricted subset for a separate brief | Completed, separate subset |

The 479-target and 500-target records must not be treated as interchangeable.

The 368-target subset must not be presented as the complete 500-target evaluation.

## 9. Comparative benchmark experiments

| ID | Method | Population | Main reported outcome | Status |
|---|---|---:|---|---|
| B-01 | PAC-CF V3 | 479 | 473 successful counterfactuals | Reported |
| B-02 | PAC-CF V3 | 500 | 494 successful counterfactuals in deduplicated artifact | Reported, separate artifact |
| B-03 | CoDy | 479 | 290 successful outputs | Reported |
| B-04 | TemGX | 479 | 109 strictly verified fidelity results | Reported |
| B-05 | Strict comparative filter | 479 | PAC-CF 471, CoDy 273, TemGX 104 | Reported, filter details required |

The broad success counts and strict comparative figures use different result filters.

A publication-ready comparison must define the exact filter applied to every method.

## 10. Runtime experiments

### 10.1 TGAT batch-scoring throughput

| ID | Batch size | Throughput |
|---|---:|---:|
| R-01 | 1 | 129.10 candidates/s |
| R-02 | 2 | 186.16 candidates/s |
| R-03 | 4 | 495.43 candidates/s |
| R-04 | 8 | 833.55 candidates/s |
| R-05 | 16 | 1,459.89 candidates/s |
| R-06 | 32 | 3,239.92 candidates/s |

Reported score-equivalence differences were zero for the tested benchmark inputs.

### 10.2 Baseline runtime

| Method | Total runtime | Mean runtime per target |
|---|---:|---:|
| CoDy | 115.26 s | 0.2287 s |
| TemGX | 14.05 s | 0.0117 s |

These measurements should not be treated as a fully controlled runtime comparison unless hardware, batching, preprocessing, graph construction, and timing boundaries are verified as equivalent.

### 10.3 End-to-end runtime

An estimated runtime of approximately 11.6 minutes for 500 PAC-CF targets was reported.

A complete measured end-to-end runtime experiment remains necessary, including:

- Proposal generation.
- Projection.
- Exact search.
- TGAT evaluation.
- Independent validation.
- Result serialization.

## 11. Predictor-aware localization experiment

| ID | Experiment | Target count | Result |
|---|---|---:|---|
| L-01 | Local versus global graph evaluation | 10 | Maximum absolute score difference 3.209 × 10⁻⁴ |

Reported local graph sizes were 3–30 edges, compared with global graph sizes of approximately 0.68–4.6 million edges.

This is preliminary evidence only.

A larger evaluation population and explicit error analysis are required before making broad localization-equivalence claims.

## 12. Required follow-up experiments

The following experiments are recommended before making stronger research claims.

| ID | Required experiment | Why it is needed | Priority |
|---|---|---|---|
| F-01 | Reconcile 479-target outcome and status counts | Resolve inconsistent result partitions | Critical |
| F-02 | Reconcile 479- and 500-target artifacts | Establish exact population and provenance relationship | Critical |
| F-03 | Reproduce all baseline metrics with common filters | Ensure fair comparison | Critical |
| F-04 | Independent final-mask rescoring | Strengthen score-consistency evidence | High |
| F-05 | Expand WFFC adversarial tests | Test edge cases beyond synthetic examples | High |
| F-06 | Larger local/global localization study | Evaluate approximation error more broadly | High |
| F-07 | End-to-end runtime measurement | Replace projected runtime with measured runtime | High |
| F-08 | Ablation study | Identify contribution of proposal, projection, exact search, and validation | High |
| F-09 | Threshold sensitivity analysis | Assess dependence on the fixed threshold | High |
| F-10 | Candidate-window sensitivity analysis | Assess dependence on historical search context | High |
| F-11 | More random seeds | Strengthen model and explanation stability analysis | Medium |
| F-12 | External or real-world dataset evaluation | Assess transfer beyond synthetic AMLSim data | Future work |

## 13. Reproducibility requirements

Every completed experiment should preserve:

1. Experiment identifier.
2. Source-code commit or revision.
3. Dataset and manifest hashes.
4. Model checkpoint hash.
5. Configuration file.
6. Random seed.
7. Environment information.
8. Input and output artifact hashes.
9. Raw per-target records.
10. Test and validation reports.
11. Known defects or exclusions.
12. Exact metric definitions.

Aggregate metrics without per-target records are insufficient for complete independent reproduction.

## 14. Reporting rules

The following rules apply to all tables and figures derived from this matrix.

- Always state the denominator.
- Identify the result artifact.
- Distinguish successful counterfactuals from already-safe targets.
- Distinguish native baseline outputs from post-processed outputs.
- Distinguish execution-path consistency from independent validation.
- Distinguish bounded certification from unrestricted global optimality.
- Do not merge results from different thresholds or candidate windows.
- Do not present synthetic validation as real-world deployment evidence.
- Do not silently reconcile inconsistent counts.
- Label estimates separately from measured values.

## 15. Conclusion

The experiment matrix records the development path from V1 through V3, the feasibility and invariant tests, the reconstructed model evaluation, and the comparative benchmarks.

The main remaining evidence priorities are the reconciliation of result populations, common-filter baseline evaluation, independent final-mask verification, and complete end-to-end measurement.

These issues must be resolved before the reported aggregate results are treated as a fully reconciled publication-ready benchmark.
