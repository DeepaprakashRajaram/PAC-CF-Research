# Development History

## 1. Purpose

This document records the major development stages of the PAC-CF research project.

It distinguishes model development, application integration, counterfactual methodology revisions, validation work, and archival recovery.

The history is intended to preserve both successful and unsuccessful approaches.

## 2. Original research foundation

The project began with a temporal graph learning pipeline for financial transaction analysis.

The principal predictor was a Temporal Graph Attention Network (TGAT) trained using the IBM AMLSim HI-Small synthetic transaction dataset.

The original model configuration used:

- 16-dimensional node features.
- 39-dimensional edge features.
- 64-dimensional hidden dimension.
- 64-dimensional time dimension.
- 4 attention heads.

The original A4 and A5 experiments produced historical validation reports.

The final original A4 and A5 checkpoints were later lost during a cleanup incident.

## 3. Checkpoint deletion and recovery investigation

During release integrity testing, a legacy test file, `tests/test_checkpoint_resume.py`, contained cleanup behavior that deleted the `research/checkpoints` directory.

This removed the original final A4 and A5 checkpoint files.

A later backup investigation recovered an earlier checkpoint from a project archive.

The recovered checkpoint was not the final A4 checkpoint.

The backup archive did not contain the final A5 checkpoint.

The investigation therefore concluded that the original final A4 and A5 checkpoints could not be recovered from the available backup.

This incident established the need for immutable checkpoint handling and explicit artifact-safety gates.

## 4. A4-equivalent reconstruction

A later TGAT reconstruction was trained from scratch using the recorded architecture and experimental configuration.

The resulting checkpoint was stored in:

```text
research/reconstruction/a4/run_final/tgat_checkpoint_latest.pt
```

Recorded SHA-256:

```text
1CE6BB6C2D1C3239C88DE862E9D66A2D0A22894993542F27343D17F57CE18FE5
```

This reconstruction was used to continue downstream research.

It must not be described as the recovered original A4 checkpoint.

A multi-seed reconstruction was also recorded for seeds 42–45.

## 5. B0–B7 application development

The project then developed an application pipeline around the frozen TGAT predictor.

### B0 — Architecture audit

The initial audit identified that the research repository did not yet contain a complete application architecture.

The audit established a B1–B7 implementation roadmap.

### B1 — Exact inference integration

B1 integrated the frozen TGAT inference behavior into the application.

Key work included:

- Feature schemas.
- Preprocessing.
- Graph-context construction.
- Model inference.
- CPU fallback.
- Inference parity testing.

A documented parity test matched a frozen validation target logit:

```text
-4.9926300048828125
```

### B2 — Graph state and live context

B2 introduced graph-state management and live graph context.

The lifecycle was:

1. Construct historical context.
2. Temporarily append the incoming transaction for inference.
3. Run inference.
4. Ingest the transaction only after successful inference.

This was designed to prevent failed inference from mutating persistent graph state.

### B3 — API layer

B3 introduced FastAPI endpoints:

- `GET /health`
- `GET /graph/stats`
- `POST /transactions/analyze`
- `GET /transactions/{transaction_id}`
- `GET /transactions/{transaction_id}/explanation`
- `GET /transactions/{transaction_id}/graph`

The implementation used inference-before-mutation behavior for failure atomicity.

### B4 — Explainability

B4 added Integrated Gradients and independent investigation heuristics.

The documented Integrated Gradients implementation used:

- A zero baseline.
- 50 integration steps.
- 39-dimensional edge attribution.
- Frozen model parameters.
- Gradients through detached interpolated input leaves.

The explanation snapshot captured the context before ingestion.

Independent heuristics included velocity, anomalous amount, and structural novelty.

These heuristics were kept distinct from the model's learned prediction.

### B5 — Frontend

B5 introduced a React and Vite interface.

The intended workflow included:

1. Transaction input.
2. API analysis.
3. Model logit and sigmoid transformation.
4. Asynchronous explanation.
5. Feature attribution.
6. Independent investigation rules.
7. Historical context.
8. Graph inspection.

The interface was designed to distinguish model output from attribution and investigation context.

### B6 — Graph explanation

B6 introduced graph visualization using immutable explanation snapshots.

The graph response contained historical context and the investigated transaction overlay.

The interface supported:

- Zoom and pan.
- Fit-to-view.
- Node selection.
- Highlighting and dimming.
- Factual metadata inspection.

The terminology was deliberately constrained. Descriptions such as “Criminal Network,” “Fraud Path,” and “Attack Chain” were avoided because graph relationships do not establish criminality.

### B7 — End-to-end validation

B7 tested the complete React-to-FastAPI-to-graph-to-model workflow.

The documented validation areas included:

- API parity.
- Deterministic snapshots.
- Future-contamination defense.
- Failure resilience.
- Score fidelity.
- Scientific wording.
- Browser workflow.
- Build integrity.

## 6. Release hardening

Later hardening addressed:

- Temporal sorting by `(rel_minute, internal_idx)`.
- Rejection of NaN and infinite values.
- Integration of manual cases into automated regression.
- In-memory transaction-store limitations.
- Duplicate transaction facts receiving separate UUID-backed edges.

The recorded release-hardening report stated that three identified defects were resolved and no remaining defects were reported in that audit.

This is a historical audit statement, not a guarantee that the application is defect-free.

## 7. Counterfactual research development

### 7.1 CCO foundation

Counterfactual Combinatorial Optimization (CCO) was developed as the broader explanatory optimization strategy.

The initial formulation searched for small historical transaction sets whose removal changed the frozen TGAT prediction while satisfying temporal and flow constraints.

### 7.2 Initial WFFC formulation

The initial WFFC implementation used zero starting balances.

Synthetic tests exposed cases where factual outgoing transactions became infeasible because the dataset did not provide complete initial account balances.

This led to the relative causal-flow / maximum-deficit initialization approach.

The initial balance was calculated on the factual graph and frozen for counterfactual evaluation.

### 7.3 PAC-CF V1

V1 used a continuous optimization proposal followed by a feasibility and discrete intervention process.

The recorded development run explored 81 configurations.

The selected configuration achieved 58% development success and 45.6% evaluation success.

Subsequent analysis identified defects involving random sampling, fractional masks, and score handling.

V1 was retained as a historical development stage.

### 7.4 PAC-CF V2

V2 introduced deterministic per-target seeds, a discrete causal cascade simulator, and independent exhaustive-oracle testing.

Its development-set success was high.

However, the evaluation identified a stale-score defect: a projected final mask could differ from the mask used to calculate the reported score.

V2 was therefore frozen as an invalidated historical version.

### 7.5 PAC-CF V3

V3 formalized the pipeline:

**Proposal → Feasibility Projection → Exact TGAT Evaluation → Certified/Bounded Search**

Its design introduced:

- Exact final-mask evaluation.
- Mask-hash score caching.
- Discrete bounded search.
- Explicit result statuses.
- Independent WFFC validation.
- Certification limited to an exhaustively searched bounded space.
- Heuristic fallback when exact search did not produce a certificate.

Synthetic invariant testing included 20 documented checks.

Historical V3 result artifacts contain both 479-target and 500-target records. Their relationship and a status-count inconsistency must be resolved before publication.

## 8. Baseline comparison work

The research considered CoDy and TemGX.

The comparison work distinguished native baseline outputs from post-hoc WFFC validation and strict independent fidelity checks.

The methods have different native search procedures and should not be assumed to be directly equivalent.

## 9. Runtime investigation

A GPU inference benchmark evaluated multiple candidate batch sizes on an RTX 3060 Laptop GPU.

The recorded throughput increased with batch size.

A projected 500-target runtime was estimated from the scoring benchmark, but projection time was not measured in that estimate.

The estimate must therefore not be treated as a complete end-to-end runtime measurement.

## 10. Current archival state

The project is being preserved in a public research repository.

The original project environment and some artifacts were lost following SSD failure and the checkpoint cleanup incident.

The repository should therefore distinguish:

- Original historical reports.
- Reconstructed model artifacts.
- Later application implementation records.
- Counterfactual algorithm revisions.
- Historical experiment results.
- Claims awaiting independent reproduction.

## 11. Development principle

The project history should not be rewritten to make the final method appear to have been developed without failures.

Invalidated approaches, checkpoint loss, protocol revisions, and evaluation defects are part of the research record.

Preserving them improves the transparency and credibility of the work.
