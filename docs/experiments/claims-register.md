# PAC-CF Research Claims Register

## 1. Purpose

This document defines the claims that may be made about Projection-Aware Certified Counterfactual Search (PAC-CF), the evidence supporting those claims, and the limitations that must accompany them.

Its purpose is to maintain scientific integrity throughout:

- Research papers.
- Technical reports.
- Presentations.
- Demonstrations.
- GitHub documentation.
- Professor and reviewer discussions.

A claim must not be stronger than its supporting evidence.

The register distinguishes mathematical properties, implementation properties, empirical observations, comparative findings, and future research hypotheses.

## 2. Evidence classification

Use the following evidence levels.

| Level | Meaning |
|---|---|
| E0 — Design statement | A design intention or architectural description |
| E1 — Mathematical argument | A property derived under explicitly stated assumptions |
| E2 — Synthetic validation | A property tested on controlled synthetic examples |
| E3 — Experimental observation | A result observed in a documented experimental run |
| E4 — Independent validation | A result checked by an independent implementation or validation procedure |
| E5 — External reproduction | A result independently reproduced by an external research group |

These levels describe evidence strength, not a universal ranking of scientific quality.

A claim can have different evidence levels for different parts. For example, an algorithm may have a mathematical argument for a property and only synthetic validation of its implementation.

No external reproduction is currently established by this register.

## 3. Core contribution claims

### C-01: Projection-aware counterfactual search

**Claim:** PAC-CF separates candidate proposal, feasibility projection, exact predictor evaluation, and bounded discrete search.

- Evidence: Architecture and implementation design.
- Level: E0/E3.
- Permitted wording: "PAC-CF uses a projection-aware, multi-stage counterfactual search pipeline."
- Avoid: "PAC-CF is the first method to combine these components" without a comprehensive literature review.

### C-02: Exact final-mask evaluation

**Claim:** PAC-CF evaluates the final projected intervention rather than reusing a score computed for a different proposal mask.

- Evidence: V3 design and score-consistency checks.
- Level: E3; E4 only to the extent that independent final-mask validation is documented.
- Permitted wording: "The V3 execution path evaluates the final projected intervention mask before reporting its score."
- Avoid: "The implementation is mathematically incapable of producing stale scores" without exhaustive code-level proof and regression evidence.

### C-03: Bounded minimum-cardinality certification

**Claim:** PAC-CF can certify minimum intervention cardinality within an exhaustively checked finite candidate space.

- Evidence: Bounded exact search and certification logic.
- Level: E1/E3, subject to implementation verification.
- Permitted wording: "PAC-CF establishes minimum cardinality within the declared candidate space when all smaller cardinalities have been exhaustively checked."
- Avoid: "PAC-CF always finds the globally optimal counterfactual."

### C-04: Feasibility-aware explanations

**Claim:** PAC-CF incorporates WFFC constraints into counterfactual search.

- Evidence: WFFC specification, synthetic tests, and reported validator results.
- Level: E1/E2/E3.
- Permitted wording: "PAC-CF applies a partial-observability flow-consistency constraint to historical transaction interventions."
- Avoid: "PAC-CF verifies actual bank balances."

### C-05: Deterministic search

**Claim:** PAC-CF V3 is designed for deterministic target-specific execution.

- Evidence: Target-specific seeds, deterministic ordering, and invariant tests.
- Level: E2/E3.
- Permitted wording: "The implementation uses target-specific seeds and deterministic event ordering to support reproducibility."
- Avoid: "Every execution is guaranteed to be bitwise identical across all hardware and software environments."

## 4. WFFC claims

### C-06: WFFC feasibility

**Claim:** WFFC checks partial-observability flow consistency under a declared initialization and chronological processing model.

- Evidence: Mathematical specification and synthetic validation.
- Level: E1/E2.
- Permitted wording: "The intervention satisfies the implemented WFFC flow-consistency constraints."
- Avoid: "The intervention is proven financially possible in the real banking system."

### C-07: Maximum-deficit initialization

**Claim:** Initial modeled balances are derived from maximum observed historical flow deficits and frozen during intervention evaluation.

- Evidence: WFFC design and implementation description.
- Level: E0/E3.
- Permitted wording: "The method uses a frozen maximum-deficit initialization to address missing starting-balance observations."
- Avoid: "The inferred initial balances are the actual balances held by the accounts."

### C-08: Projection idempotence

**Claim:** Under fixed initial conditions and deterministic processing, the WFFC projection is intended to satisfy idempotence.

\[
P(P(M))=P(M)
\]

- Evidence: Mathematical argument and synthetic invariant testing.
- Level: E1/E2.
- Permitted wording: "Idempotence is a specified projection property supported by the documented synthetic tests."
- Avoid: "Idempotence has been proven for every possible transaction graph."

### C-09: Projection monotonicity

**Claim:** Under the declared flow model and fixed initial balances, the projection is intended to preserve component-wise ordering.

\[
M_1\leq M_2
\Rightarrow
P(M_1)\leq P(M_2)
\]

- Evidence: Mathematical argument and synthetic invariant testing.
- Level: E1/E2.
- Permitted wording: "The projection is designed to be monotone under the stated assumptions."
- Avoid: "All financial feasibility systems are monotone."

### C-10: Temporal causality

**Claim:** The WFFC processing order prevents later inflows from funding earlier outgoing transactions.

- Evidence: Chronological processing and temporal starvation tests.
- Level: E2/E3.
- Permitted wording: "The tested WFFC implementation enforces chronological flow processing."
- Avoid: "The model reconstructs the true temporal order of every real-world bank event."

## 5. Predictor fidelity and explanation claims

### C-11: Model-level counterfactual fidelity

**Claim:** A successful counterfactual changes the frozen target model's decision under the configured threshold.

- Evidence: Exact TGAT evaluation and reported result records.
- Level: E3; E4 where independent rescoring exists.
- Permitted wording: "The intervention changes the frozen TGAT prediction to the configured safe side of the threshold."
- Avoid: "The intervention proves the transaction was legitimate."

### C-12: Score-mask consistency

**Claim:** The returned score is associated with the exact returned intervention mask.

- Evidence: V3 final-mask evaluation and reported consistency checks.
- Level: E3; E4 only where independent rescoring is available.
- Permitted wording: "The reported score is computed for the final returned intervention mask."
- Avoid: "Score consistency proves the counterfactual is causally correct."

### C-13: Integrated Gradients attribution

**Claim:** Integrated Gradients provides feature-level attribution relative to the selected baseline and model.

- Evidence: Attribution implementation and numerical procedure.
- Level: E0/E3.
- Permitted wording: "Integrated Gradients estimates feature attribution relative to the specified baseline."
- Avoid: "Integrated Gradients proves that an individual transaction caused the model decision."

### C-14: Graph context

**Claim:** The graph visualization presents historical transaction context and the investigated transaction overlay.

- Evidence: B6 graph snapshot and visualization design.
- Level: E3.
- Permitted wording: "The graph displays historical transaction relationships associated with the investigated target."
- Avoid: "The graph proves a criminal network or an intentional fraud chain."

## 6. Empirical performance claims

### C-15: Reconstructed TGAT performance

**Claim:** The reconstructed A4-equivalent TGAT achieved a mean validation ROC-AUC of approximately 0.9427 over four seeds.

- Evidence: Four reported seed results.
- Level: E3.
- Permitted wording: "Across four reconstructed runs, the mean validation ROC-AUC was approximately 0.9427."
- Avoid: "The original A4 checkpoint was recovered."
- Avoid: "The original A4 result was independently reproduced exactly."

The original A4 and A5 checkpoints were lost. The reconstructed model is a separate artifact.

### C-16: PAC-CF V3 success rate

**Claim:** The separate deduplicated 500-target artifact reports 494 successful counterfactuals.

- Evidence: Deduplicated artifact and its reported hash.
- Level: E3, subject to artifact-level reconciliation.
- Permitted wording: "The deduplicated 500-target artifact reports 494 successful counterfactuals (98.8%)."
- Avoid: "The 479-target and 500-target results are the same experiment."
- Avoid: "The final success rate is unambiguously 98.8% across every result record."

The 479-target primary outcome record reports 473 successful counterfactuals, while its status summary has a separate count discrepancy. These records must remain distinct until reconciled.

### C-17: PAC-CF strict comparative result

**Claim:** A strict comparative record reports 471/479 PAC-CF results, 273/479 CoDy results, and 104/479 TemGX results.

- Evidence: Reported strict-filter comparison.
- Level: E3.
- Permitted wording: "Under the reported strict comparison filter, PAC-CF recorded 471/479, CoDy 273/479, and TemGX 104/479."
- Avoid: "PAC-CF universally outperforms all temporal graph explainers."

The exact common filter definition must accompany publication of these figures.

### C-18: CoDy comparison

**Claim:** The tested CoDy execution produced 290 successful outputs among 479 targets.

- Evidence: Reported benchmark results.
- Level: E3.
- Permitted wording: "The tested CoDy execution reported 290 successful outputs among 479 targets."
- Avoid: "The original CoDy method has a universal success rate of 60.5%."

### C-19: TemGX comparison

**Claim:** The tested TemGX execution produced 109 independently verified fidelity results among 479 targets.

- Evidence: Reported benchmark results and independent fidelity checks.
- Level: E3/E4, depending on the retained validation records.
- Permitted wording: "The tested TemGX execution achieved strict fidelity on 109 of 479 targets."
- Avoid: "TemGX is fundamentally incorrect."

The observed complement-selection concern is limited to the tested implementation and execution path.

### C-20: GPU throughput

**Claim:** TGAT candidate-scoring throughput reached approximately 3,239.92 candidates per second at batch size 32 in the documented environment.

- Evidence: Reported GPU benchmark.
- Level: E3.
- Permitted wording: "The measured batch-size-32 throughput was approximately 3,239.92 candidates per second on the documented GPU environment."
- Avoid: "PAC-CF can process any 500-target workload in exactly 11.6 minutes."

The 500-target runtime was an estimate, not a complete end-to-end measurement.

### C-21: Predictor-aware localization

**Claim:** A 10-target experiment reported a maximum absolute score difference of approximately \(3.209\times10^{-4}\) between local and global evaluation.

- Evidence: Reported localization experiment.
- Level: E3.
- Permitted wording: "In a 10-target localization experiment, the maximum observed absolute score difference was approximately \(3.209\times10^{-4}\)."
- Avoid: "Local inference is exactly equivalent to global inference."
- Avoid: "Localization has zero error or constant-time complexity."

## 7. Claims that are not currently supported

The following statements must not be used as established findings.

| Unsupported claim | Reason |
|---|---|
| PAC-CF is the first method of its kind | Comprehensive novelty verification is not established |
| PAC-CF proves real-world transaction causality | Model counterfactuals do not establish real-world causation |
| WFFC reconstructs actual account balances | Starting balances are not available |
| Every certified result is globally optimal over all historical transactions | Certification is bounded by the candidate space |
| TGAT scores are calibrated fraud probabilities | Calibration has not been established |
| High-risk model output proves criminal activity | Model output is not a legal or investigative finding |
| Graph relationships prove collusion | Relationships do not establish intent |
| Integrated Gradients proves causal feature importance | Attribution is not causal proof |
| PAC-CF is ready for operational police or bank deployment | Real-world validation, governance, and operational testing remain necessary |
| The reconstructed A4-equivalent checkpoint is the original A4 | The original artifact was lost |

## 8. Novelty and literature positioning

The current defensible research contribution is the combination of:

1. Partial-observability temporal flow-consistency projection for AML transaction interventions.
2. Exact frozen-predictor evaluation after projection.
3. Bounded discrete minimum-cardinality search.
4. Explicit certification boundaries.
5. Independent feasibility and score validation.

This combination may offer a useful contribution to constrained temporal graph counterfactual explanation.

However, the phrase "first" or "novel for the first time" requires a comprehensive literature review.

The literature comparison should include:

- CoDy.
- TemGX.
- Temporal graph counterfactual explanation methods.
- Constrained graph counterfactual methods.
- Financial transaction flow and AML explanation methods.
- Discrete minimum-cardinality counterfactual search.

A distinction must be maintained between novelty of an individual component and novelty of the complete combination.

## 9. Approved terminology

Use the following terminology consistently.

| Prefer | Avoid |
|---|---|
| Model risk score | Calibrated fraud probability |
| Configured safe-side model decision | Proven legitimate transaction |
| Historical transaction context | Criminal network |
| WFFC flow consistency | Actual bank balance verification |
| Counterfactual intervention | Real-world causal explanation |
| Integrated Gradients attribution | Proven causal contribution |
| Bounded minimum-cardinality certificate | Unrestricted global optimum |
| Tested implementation | Universal method guarantee |
| Synthetic AML transaction dataset | Real bank transaction dataset |
| Investigative lead | Confirmed criminal activity |

## 10. Publication readiness checklist

Before submitting a paper or presenting definitive claims, verify:

- [ ] Every reported metric has a source result artifact.
- [ ] Every result identifies its population and denominator.
- [ ] The 479-target and 500-target records are reconciled or explicitly separated.
- [ ] The certification-status discrepancy is resolved.
- [ ] Baseline comparisons use a common, documented filter.
- [ ] Final-mask scores are independently verified.
- [ ] WFFC assumptions and limitations are explicit.
- [ ] Bounded certification is not described as unrestricted global optimality.
- [ ] Model reconstruction is not described as original-checkpoint recovery.
- [ ] Synthetic-data limitations are disclosed.
- [ ] Statistical uncertainty is reported where appropriate.
- [ ] Novelty claims are supported by a comprehensive literature review.
- [ ] All figures and tables can be traced to reproducible source records.
- [ ] Operational claims are separated from research prototype capabilities.

## 11. Final scientific position

PAC-CF should be presented as a research framework for generating constrained, model-level counterfactual explanations for temporal graph neural networks.

Its strongest defensible contributions are architectural and methodological:

- Feasibility projection is separated from candidate proposal.
- The final projected intervention is evaluated exactly.
- Discrete search supports bounded minimum-cardinality certification.
- Validation and certification statuses make evidence boundaries explicit.

Its empirical results on IBM AMLSim HI-Small are promising but remain subject to the documented artifact and population discrepancies.

PAC-CF does not establish actual financial balances, real-world transaction causality, criminal intent, or confirmed fraud.

The governing rule for every future research claim is:

**Report what the evidence demonstrates, identify the assumptions under which it holds, and never present an implementation observation as a universal mathematical guarantee.**
