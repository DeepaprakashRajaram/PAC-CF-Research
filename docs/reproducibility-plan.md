# Reproducibility Plan

## 1. Objective

This document defines the work required to reproduce the PAC-CF research from the available documentation and artifacts.

The original project environment experienced SSD failure and loss of original checkpoint files.

The reproducibility effort must therefore distinguish exact reproduction from reconstruction.

The goal is to make the research procedure independently repeatable while preserving the historical status of unavailable artifacts.

## 2. Reproduction levels

Use the following levels when reporting reproduction progress.

| Level | Description |
|---|---|
| R0 — Documentation available | Research design and methodology are documented. |
| R1 — Inputs identified | Dataset, model configuration, target manifest, and dependencies are identified. |
| R2 — Environment recreated | Software and hardware environment is recreated or documented. |
| R3 — Component reproduction | Individual preprocessing, model, projection, or validation components are reproduced. |
| R4 — End-to-end reproduction | The complete evaluation pipeline is executed from identified inputs. |
| R5 — Independent verification | Results are independently checked using separate validation procedures and traceable artifacts. |

A higher level should not be claimed unless the required evidence is preserved.

## 3. Required inputs

A complete reproduction requires:

### Dataset

- IBM AMLSim HI-Small source data.
- Exact cleaned dataset.
- Dataset preprocessing code.
- Dataset hash.
- Category mappings.
- Account and transaction identifiers.

### Model

- TGAT architecture.
- Exact checkpoint.
- Feature preprocessing.
- Temporal neighborhood construction.
- Inference code.
- Model hash.

The original final A4/A5 checkpoints are unavailable.

A reproduction using the reconstructed A4-equivalent checkpoint must be explicitly labeled as a reconstruction-based reproduction.

### Evaluation population

- Target manifest.
- TP/FP definitions.
- Target-selection seed.
- Evaluation threshold.
- Development/evaluation split.
- Manifest hash.

### Counterfactual implementation

- PAC-CF version.
- Proposal configuration.
- WFFC projection.
- Exact search parameters.
- Heuristic fallback configuration.
- Independent validators.
- Result serialization format.

## 4. Environment record

The historical reconstructed training environment was recorded as:

- Python 3.14.6.
- PyTorch 2.14.0+cu132.
- CUDA 13.2.
- RTX 3060 Laptop GPU.

A later runtime benchmark used:

- Python 3.14.7.
- PyTorch 2.14.0+cu132.
- CUDA enabled.
- RTX 3060 Laptop GPU.

These are historical environment records.

A new reproduction must capture the actual environment used rather than assuming it is identical.

Record:

- Operating system.
- Python version.
- PyTorch version.
- CUDA runtime and driver.
- GPU model and memory.
- CPU model.
- RAM.
- Dependency lockfile.
- Git commit.
- Checkpoint hash.
- Dataset hash.

## 5. Reproduction sequence

### Phase 1 — Artifact audit

1. Inventory all available files.
2. Identify original, recovered, reconstructed, and generated artifacts.
3. Calculate SHA-256 hashes.
4. Identify missing artifacts.
5. Record invalidated experiment outputs.
6. Freeze the audit report.

Do not overwrite original evidence during this phase.

### Phase 2 — Dataset verification

1. Obtain the authorized AMLSim data.
2. Reproduce the preprocessing pipeline.
3. Verify account and transaction counts.
4. Verify category mappings.
5. Calculate the cleaned dataset hash.
6. Compare it with the recorded dataset hash.

If the hash differs, document the difference before proceeding.

### Phase 3 — Model verification

1. Reconstruct the TGAT architecture.
2. Verify node and edge dimensions.
3. Verify feature transformations.
4. Verify temporal ordering.
5. Load the explicitly identified checkpoint.
6. Run frozen-target inference.
7. Compare outputs with available parity evidence.

Do not claim exact reproduction of original A4 if using the reconstructed checkpoint.

### Phase 4 — Target manifest verification

1. Recreate the target-selection procedure.
2. Verify TP and FP definitions.
3. Verify target counts.
4. Verify development/evaluation separation.
5. Verify deterministic seed behavior.
6. Calculate the manifest hash.
7. Confirm that target identities are unique.

### Phase 5 — WFFC reproduction

1. Reimplement the frozen maximum-deficit initialization.
2. Preserve deterministic event ordering.
3. Implement the causal flow projection.
4. Implement an independent validator.
5. Recreate synthetic oracle cases.
6. Test factual-mask identity.
7. Test idempotence.
8. Test causal cascade behavior.
9. Test monotonicity under the documented assumptions.
10. Preserve all test outputs.

### Phase 6 — PAC-CF reproduction

1. Freeze the predictor and target context.
2. Recreate proposal generation.
3. Recreate WFFC projection.
4. Recreate exact final-mask scoring.
5. Recreate mask-hash caching.
6. Recreate bounded discrete search.
7. Recreate heuristic fallback.
8. Verify status assignment.
9. Run independent final validation.
10. Preserve machine-readable results.

### Phase 7 — Baseline reproduction

For CoDy and TemGX:

1. Identify the exact publication and implementation version.
2. Use the documented native algorithm.
3. Record baseline configuration.
4. Use the same target population where possible.
5. Use the same predictor and graph context where supported.
6. Apply the same independent validation procedure.
7. Separate native output from post-hoc filtering.
8. Record runtime boundaries.

Do not silently modify a baseline algorithm and still describe it as the original method.

### Phase 8 — Statistical analysis

1. Verify all denominators.
2. Separate successes, failures, and already-safe targets.
3. Calculate confidence intervals.
4. Calculate intervention-size summaries.
5. Perform paired tests only when pairing is valid.
6. Preserve per-target results.
7. Verify all summary tables against raw data.

### Phase 9 — Independent audit

An independent audit should verify:

- Dataset identity.
- Checkpoint identity.
- Target identity.
- Model score.
- Final intervention mask.
- WFFC validity.
- Temporal validity.
- Counterfactual threshold.
- Search status.
- Certification conditions.
- Result serialization.

The independent validator should not rely solely on the search implementation's own success flags.

## 6. Required invariants

The following invariants must be tested.

### Predictor invariants

- Model parameters remain frozen.
- Inference uses the declared checkpoint.
- Preprocessing is deterministic.
- Target context is fixed.

### Temporal invariants

- No future edge is used.
- Event ordering is deterministic.
- Equal timestamps use the declared tie-breaking rule.

### WFFC invariants

- Factual-mask identity under the declared assumptions.
- Frozen initial balances.
- Deterministic projection.
- Idempotence.
- Causal cascade behavior.
- Independent validation.

### PAC-CF invariants

- Final mask is binary.
- Final score matches final mask.
- Mask cache uses exact identity.
- Duplicate final masks are deduplicated.
- Exact search covers the declared smaller-cardinality space.
- Heuristic results are not mislabeled as certified.

## 7. Required result artifacts

Each reproduction should preserve:

```text
reproduction/
├── environment/
│   ├── system-info.txt
│   ├── dependencies.txt
│   └── hardware-info.txt
├── manifests/
│   ├── dataset-manifest.json
│   ├── target-manifest.json
│   └── checkpoint-manifest.json
├── configs/
│   └── experiment-config.json
├── logs/
│   └── execution.log
├── results/
│   ├── per-target-results.jsonl
│   ├── summary.json
│   └── metrics.csv
├── validation/
│   ├── wffc-validation.json
│   ├── temporal-validation.json
│   └── score-consistency.json
└── provenance/
    └── sha256-manifest.txt
```

This is a proposed structure for future reproduction artifacts, not a claim that these files already exist.

## 8. Result reconciliation

The recorded V3 evidence includes 479-target and 500-target summaries.

The status counts in one 479-target summary do not reconcile with the reported success count.

Before publishing:

1. Retrieve the primary per-target result artifacts.
2. Recompute unique target count.
3. Recompute success count.
4. Recompute status counts.
5. Recompute WFFC and score-consistency rates.
6. Compare raw and deduplicated files.
7. Document any exclusions.
8. Generate the final summary directly from verified records.

Do not resolve inconsistencies by manually editing summary tables.

## 9. Reproduction reporting template

Every reproduction report should contain:

- Reproduction identifier.
- Date.
- Git commit.
- Research version.
- Dataset identity.
- Checkpoint identity.
- Target manifest identity.
- Environment.
- Configuration.
- Execution command.
- Runtime.
- Result artifact hashes.
- Independent validation results.
- Deviations from historical methodology.
- Known limitations.
- Reproduction level achieved.

## 10. Reproducibility claim policy

Use precise language:

- “Documentation reproduced” when only the written procedure has been recreated.
- “Component reproduced” when an individual component has been independently executed.
- “Reconstructed” when an unavailable artifact has been recreated from documented information.
- “End-to-end reproduced” only when the full procedure has been executed.
- “Independently verified” only when separate validation evidence exists.

Do not claim that the original A4/A5 checkpoints have been reproduced or recovered.

## 11. Final objective

The final goal is an auditable research pipeline in which every important result can be traced to:

1. A specific dataset.
2. A specific checkpoint.
3. A specific target population.
4. A specific configuration.
5. A specific execution.
6. A specific result artifact.
7. An independent validation procedure.

Reproducibility should be demonstrated through preserved evidence, not inferred from documentation alone.
