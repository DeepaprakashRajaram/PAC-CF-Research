# PAC-CF System Architecture

## 1. Purpose

This document describes the architecture of Projection-Aware Certified Counterfactual Search (PAC-CF), a counterfactual explanation framework designed for temporal graph neural networks in anti-money laundering (AML) transaction analysis.

The architecture separates four responsibilities:

1. Candidate intervention generation.
2. Temporal and financial feasibility projection.
3. Exact evaluation using a frozen temporal graph model.
4. Discrete search and certification.

The central design principle is that a counterfactual must be evaluated using the exact final intervention that is returned to the user.

A proposed intervention is not considered valid merely because it changes a model score before feasibility constraints are applied.

## 2. Design objectives

PAC-CF is designed around the following objectives.

### 2.1 Model fidelity

The final counterfactual must be evaluated using the frozen target TGAT model.

The explanation procedure must not silently replace, retrain, or modify the predictor.

### 2.2 Temporal validity

An intervention must respect the historical ordering of transactions.

A transaction occurring in the future cannot fund an earlier transaction.

### 2.3 Financial feasibility

The intervention must satisfy the declared WFFC feasibility model.

WFFC is a partial-observability flow-consistency approximation, not a reconstruction of actual bank balances.

### 2.4 Minimality

The search should prefer interventions containing fewer removed historical transactions.

Exact minimum-cardinality claims are permitted only when the required smaller-cardinality search space has been exhaustively checked.

### 2.5 Reproducibility

The procedure should support:

- Deterministic target-specific execution.
- Stable event ordering.
- Exact intervention-mask identification.
- Score caching.
- Independent feasibility validation.
- Explicit result statuses.

### 2.6 Auditability

Each result should be traceable to:

- The target transaction.
- The frozen model.
- The graph history.
- The final intervention mask.
- The feasibility projection.
- The final model score.
- The search procedure.
- The certification status.

## 3. High-level architecture

<escape>```text
                    Frozen Temporal Graph
                            |
                            v
                  Target TGAT Predictor
                            |
                            v
                  Target Risk Decision
                            |
                            v
                 Candidate History Window
                            |
                            v
                 Candidate Proposal Stage
                            |
                            v
                  WFFC Feasibility
                       Projection
                            |
                            v
                  Exact TGAT Evaluation
                            |
                            v
                 Discrete Search Controller
                      /           
                     /             
              Exact Search     Bounded Search
                     \             /
                      \           /
                            v
                   Final Intervention
                            |
                            v
                 Independent Validation
                            |
                            v
                  Result and Certificate
```</escape>

This is a conceptual architecture. It does not imply that every implementation stage is a separate process or service.

## 4. Input layer

### 4.1 Transaction graph

The input is a temporal transaction graph:

\[
G_t=(V,E_t,X,A)
\]

Where:

- \(V\): account nodes.
- \(E_t\): transaction edges available by time \(t\).
- \(X\): node features.
- \(A\): edge attributes.

Each transaction edge contains the information required by the target model and feasibility procedure, including source account, destination account, amount, and transaction time.

### 4.2 Target transaction

The target transaction is the transaction whose model decision is being explained.

The target is kept distinct from the historical intervention candidates.

The candidate history must not include information that occurs after the target transaction.

### 4.3 Frozen predictor

The target TGAT model is treated as a fixed prediction function:

\[
p=f_\theta(G_t,e_q)
\]

Where:

- \(\theta\) represents frozen model parameters.
- \(G_t\) represents the available historical graph.
- \(e_q\) represents the target transaction.

The model parameters remain unchanged throughout counterfactual search.

## 5. Candidate proposal stage

The proposal stage identifies potentially useful historical transactions for intervention.

A candidate intervention is represented by a mask:

\[
M\in\{0,1\}^{K}
\]

Where:

- \(K\) is the number of candidate historical edges.
- \(M_i=1\) means the edge is retained.
- \(M_i=0\) means the edge is removed.

A relaxed proposal may use:

\[
M\in[0,1]^K
\]

The continuous proposal is used to guide search. It is not itself the final discrete counterfactual.

The proposal stage may use gradient information from the frozen predictor.

Gradients guide candidate ranking; they do not establish that a proposed intervention is feasible or optimal.

## 6. WFFC feasibility projection

The proposal is passed through the Weak Factual Flow Consistency (WFFC) projection.

Its purpose is to enforce the declared temporal and partial-observability flow constraints.

The projection may modify the proposed mask by removing additional edges required to satisfy the feasibility model.

This means the projected intervention can differ from the original proposal.

Consequently, the original proposal score cannot be reused as the final counterfactual score.

The projected mask must be evaluated again.

Detailed equations and assumptions are provided in `wffc-specification.md`.

## 7. Exact TGAT evaluation

The evaluation stage receives a specific intervention mask and constructs the corresponding counterfactual graph.

The frozen TGAT model evaluates that graph.

For a final mask \(M_{\mathrm{final}}\):

\[
p_{\mathrm{cf}}
=
f_\theta(G(M_{\mathrm{final}}),e_q)
\]

The final score must correspond to the exact final mask.

The decision threshold is applied to the resulting score:

\[
p_{\mathrm{cf}}<\tau
\]

A valid counterfactual must satisfy the configured safe-side decision condition.

The score is not automatically a calibrated probability of fraud.

## 8. Discrete search controller

The search controller manages the transition from a feasible proposal to a final intervention.

### 8.1 Exact bounded-cardinality search

The exact search stage evaluates interventions in increasing cardinality.

For candidate count \(K\), the number of subsets of cardinality \(i\) is:

\[
\binom{K}{i}
\]

For a maximum exact search depth \(d\), the total number of candidate subsets is:

\[
N_{\mathrm{exact}}
=
\sum_{i=1}^{d}\binom{K}{i}
\]

For the documented PAC-CF V3 design, the exact search depth is bounded at cardinality three.

If a valid intervention is found at cardinality \(i\), and every feasible smaller-cardinality intervention has been exhaustively checked, a minimum-cardinality certificate can be issued within the declared search space.

### 8.2 Bounded heuristic search

When exact search does not establish a certified solution, a bounded heuristic search may be used.

The heuristic stage seeks a valid intervention under a computational budget.

A heuristic result must not be labelled as a certified minimum.

### 8.3 No monotonicity assumption

Removing more transactions is not assumed to always reduce the TGAT score.

Temporal graph neural networks may produce non-monotonic predictions under edge removal.

Therefore, search decisions must be based on actual model evaluations rather than an assumed monotonic relationship.

## 9. Final validation

Before a result is returned, the final intervention must pass the required validation checks.

### 9.1 Mask integrity

The final mask must be:

- Binary.
- Deterministic.
- Associated with the correct candidate ordering.
- Free of unintended duplicate candidate entries.

### 9.2 Score consistency

The stored score must be recomputed or verified against the exact final mask.

The following invariant must hold:

\[
s_{\mathrm{returned}}
=
f_\theta(G(M_{\mathrm{returned}}),e_q)
\]

Any projection that changes the mask invalidates the previous score.

### 9.3 Temporal validation

The intervention must respect the configured chronological event ordering.

### 9.4 WFFC validation

The final graph must be checked by the independent feasibility validator.

### 9.5 Certification validation

A minimum-cardinality certificate must be issued only when the required smaller-cardinality alternatives have been exhaustively checked.

## 10. Result statuses

PAC-CF uses explicit statuses to avoid treating every successful output as equally strong evidence.

| Status | Meaning |
|---|---|
| `CERTIFIED_GLOBAL_MINIMUM` | Minimum cardinality established within the exhaustively checked declared search space |
| `EXACT_SEARCH_SUCCESS_NO_CERTIFICATE` | Exact search found a valid result without establishing the required certificate |
| `HEURISTIC_COUNTERFACTUAL` | Bounded heuristic search found a valid result |
| `NO_FEASIBLE_COUNTERFACTUAL_FOUND_WITHIN_SEARCH` | No valid result found within the configured search procedure |
| `ALREADY_SAFE` | Target already satisfies the configured safe-side decision condition |

The status name `CERTIFIED_GLOBAL_MINIMUM` must not be interpreted as unrestricted global optimality over all possible interventions.

## 11. Determinism and caching

### 11.1 Target-specific random seeds

Randomized proposal operations should use deterministic target-specific seeds.

The implementation should not depend on mutable global random-number-generator state.

### 11.2 Stable event ordering

Transaction events should use a deterministic ordering.

The documented tie-breaking tuple is:

\[
(t,\ src,\ dst,\ amount,\ original\_index)
\]

This ordering ensures reproducibility when timestamps are equal.

### 11.3 Intervention cache

Exact mask hashes can be used as cache keys.

A cached score may be reused only when the following remain identical:

- Model parameters.
- Target transaction.
- Historical graph context.
- Candidate ordering.
- Intervention mask.
- Relevant inference configuration.

A cache hit must not bypass final-mask integrity checks.

## 12. Baseline integration

CoDy and TemGX are evaluated as comparison methods.

Their native candidate-generation and search procedures should remain unchanged.

Where WFFC is applied after a baseline generates an intervention, it must be reported as a separate post-processing stage.

The following should be distinguished:

1. Native baseline output.
2. Post-projected output.
3. Independent temporal validation.
4. Independent financial-feasibility validation.
5. Exact target-model fidelity evaluation.

This separation prevents a baseline's original method from being confused with the evaluation framework used in this research.

## 13. Computational considerations

The main computational cost comes from repeated TGAT inference over candidate counterfactual graphs.

Important optimization opportunities include:

- Batched candidate scoring.
- Exact-mask score caching.
- Candidate deduplication.
- Bounded exact search.
- Local temporal graph context where validated.
- Early termination when certification conditions are satisfied.

Performance optimization must preserve exact score-mask correspondence.

An optimization is not acceptable if it changes the meaning of the evaluated intervention without recording that change.

## 14. Implementation boundaries

The architecture separates conceptual responsibilities but does not establish that all components are independently deployable modules.

A production implementation should expose explicit interfaces for:

- Graph-history construction.
- Candidate generation.
- WFFC projection.
- TGAT inference.
- Discrete search.
- Independent validation.
- Result serialization.

The exact software-module boundaries should be documented against the implementation source code.

## 15. Architecture limitations

The architecture has important limitations:

1. WFFC does not reconstruct actual bank balances.
2. Synthetic AMLSim data does not establish real-world financial validity.
3. A model counterfactual is not proof of actual transaction causality.
4. Gradient-guided proposals do not establish global optimality.
5. Exact certification is bounded by the declared search space.
6. Heuristic search may fail to find an existing valid intervention.
7. A safe-side model decision does not establish that a transaction is legitimate.
8. The architecture does not independently establish criminal intent or guilt.

## 16. Summary

PAC-CF is structured to ensure that counterfactual proposals are not accepted before feasibility projection and exact model evaluation.

Its most important invariant is:

**The final returned intervention, its feasibility validation, and its reported TGAT score must all refer to the same final intervention mask.**

Its certification claims are restricted to the search space that was actually exhaustively evaluated.
