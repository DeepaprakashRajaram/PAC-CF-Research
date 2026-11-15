# PAC-CF Algorithm

## 1. Algorithm overview

PAC-CF (Projection-Aware Certified Counterfactual Search) is a constrained counterfactual search pipeline for temporal graph neural network predictions.

Its main stages are:

1. Construct the permitted historical graph context.
2. Generate a candidate intervention.
3. Project the intervention onto the implemented feasibility constraints.
4. Evaluate the exact projected intervention using the frozen predictor.
5. Search small intervention cardinalities exhaustively.
6. Use bounded heuristic search when exact search does not provide a certified result.
7. Independently validate the final intervention.
8. Return the intervention, final score, and an explicit result status.

The algorithm separates proposal generation from final validation.

## 2. Inputs

The algorithm receives:

| Input | Description |
|---|---|
| \(G_t\) | Historical transaction graph |
| \(e_q\) | Target transaction |
| \(f_\theta\) | Frozen temporal graph predictor |
| \(\tau\) | Fixed experimental decision threshold |
| \(K\) | Number of candidate historical edges |
| \(d\) | Maximum exact-search cardinality |
| \(B\) | Bounded heuristic search configuration |
| \(P\) | WFFC projection configuration |

The predictor, target transaction, temporal history, and threshold must remain fixed during a single search execution.

## 3. Candidate mask

Each candidate historical transaction is assigned a binary retention variable:

\[
M_k\in\{0,1\}
\]

The initial mask retains every candidate edge:

\[
\mathbf{M}_0=\mathbf{1}
\]

A proposal may be generated using a continuous relaxation:

\[
\tilde{M}_k\in[0,1]
\]

The continuous mask is a search proposal. It is not itself the final counterfactual.

## 4. Proposal generation

The proposal stage estimates which historical edges may be useful to remove to change the target prediction.

A differentiable objective can combine prediction fidelity and intervention sparsity.

One recorded fidelity formulation is:

\[
L_{\mathrm{fidelity}}
=
-\log(1-f_\theta(G_t(\mathbf{M}),e_q))
\]

with a sparsity term:

\[
L_{\mathrm{sparsity}}
=
\lambda\sum_k(1-M_k)
\]

The combined objective is:

\[
L=
L_{\mathrm{fidelity}}+
L_{\mathrm{sparsity}}
\]

Here, the score notation must be interpreted consistently with the model's score representation and target decision direction.

The proposal stage does not establish feasibility, final score validity, or minimum cardinality.

Gradient information is used for proposal generation and candidate ranking only.

## 5. Feasibility projection

The proposed mask is converted into a feasible intervention under the implemented WFFC rules.

The projection stage:

1. Applies the documented temporal ordering.
2. Determines the proposed retained and removed edges.
3. Applies the causal flow constraints.
4. Propagates flow limitations through downstream transactions.
5. Produces a final projected mask.
6. Records the projection outcome.

Projection can remove additional edges beyond those proposed by the optimizer.

Therefore, the projected mask may differ from the original proposal.

The model score must be recalculated after projection.

## 6. Exact final-mask evaluation

For a projected mask \(\mathbf{M}_{p}\), calculate:

\[
s_p=
\sigma(f_\theta(G_t(\mathbf{M}_{p}),e_q))
\]

The score is associated with that exact mask.

If any subsequent operation changes the mask, the score becomes stale and must be recalculated.

The implementation uses exact mask identity for score caching. A cache entry must not be reused for a different intervention.

## 7. Exact bounded discrete search

PAC-CF searches small intervention cardinalities exactly.

For each cardinality \(r\), starting from 1 and proceeding to the configured maximum \(d\):

1. Enumerate candidate subsets of size \(r\).
2. Apply the WFFC projection.
3. Validate temporal ordering.
4. Deduplicate identical final masks.
5. Evaluate each unique final mask using the frozen predictor.
6. Retain feasible candidates that satisfy the counterfactual threshold.
7. Determine whether the cardinality has been exhaustively searched.

If a valid intervention is found at cardinality \(r\), and every feasible intervention of smaller cardinality has been exhaustively checked, the result can receive a bounded minimum-cardinality certificate.

The certificate is limited to the enumerated candidate history and declared constraints.

## 8. Heuristic fallback

If exact search does not produce a certified result within the configured bound, PAC-CF may use a bounded heuristic search.

The heuristic stage uses proposal rankings and a bounded search strategy to identify candidate interventions.

A heuristic result must be evaluated using the exact final mask and independently validated.

A heuristic result is not a minimum-cardinality certificate.

## 9. Result statuses

PAC-CF uses explicit statuses to communicate the strength of the result.

| Status | Meaning |
|---|---|
| `CERTIFIED_GLOBAL_MINIMUM` | All feasible interventions of smaller cardinality were exhaustively checked within the declared search space and a valid minimum-cardinality intervention was found. |
| `EXACT_SEARCH_SUCCESS_NO_CERTIFICATE` | Exact search found a valid counterfactual, but the conditions required for a minimum-cardinality certificate were not established. |
| `HEURISTIC_COUNTERFACTUAL` | A bounded heuristic search found a valid counterfactual, without an exact minimum-cardinality guarantee. |
| `NO_FEASIBLE_COUNTERFACTUAL_FOUND_WITHIN_SEARCH` | No valid counterfactual was found within the configured search procedure. This does not prove that none exists outside that search. |
| `ALREADY_SAFE` | The target already satisfies the safe-side decision condition under the configured threshold. |

The status `CERTIFIED_GLOBAL_MINIMUM` must always be interpreted with the bounded-search qualification described above.

## 10. Final validation

Before returning a result, the algorithm verifies:

- The target identity is unchanged.
- The predictor checkpoint is unchanged.
- The temporal context is valid.
- The final mask is binary.
- The final mask satisfies the implemented WFFC validator.
- The final score corresponds to the exact final mask.
- The counterfactual satisfies the decision threshold.
- The result status matches the search evidence.
- The intervention count corresponds to the final mask.
- Duplicate masks are not counted as distinct interventions.

The result should preserve the final mask, final score, status, target identity, and validation metadata.

## 11. Complexity

For \(K\) candidate edges and exact-search depth \(d\), the number of raw subsets is:

\[
\sum_{r=1}^{d}\binom{K}{r}
\]

This can grow rapidly as \(K\) and \(d\) increase.

Projection, deduplication, model evaluation, and independent validation add additional computational cost.

The heuristic fallback is bounded by its configured search budget, but it does not provide exhaustive guarantees.

## 12. Correctness invariants

The implementation must preserve these invariants:

### Invariant 1 — Frozen predictor

Model parameters do not change during counterfactual search.

### Invariant 2 — Fixed target context

The target transaction and permitted historical context remain fixed.

### Invariant 3 — Temporal validity

No future transaction may be used to explain an earlier target.

### Invariant 4 — Projection before final scoring

The final intervention is projected before its reported score is calculated.

### Invariant 5 — Exact score-mask correspondence

The reported score must be computed from the exact returned mask.

### Invariant 6 — Independent validation

The final intervention must be checked by the designated feasibility and temporal validators.

### Invariant 7 — Bounded certification

A certification claim must identify the search space and demonstrate that all required smaller-cardinality candidates were exhaustively checked.

## 13. Interpretation

PAC-CF provides a model-level counterfactual explanation under specified assumptions.

A returned intervention indicates that the frozen predictor's decision changes when the specified historical graph edges are removed under the implemented constraints.

It does not prove that the removed transactions caused the underlying real-world activity, that the transactions were suspicious, or that the accounts involved committed wrongdoing.
