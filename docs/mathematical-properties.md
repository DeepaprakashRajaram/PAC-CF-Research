# Mathematical Properties of PAC-CF

## 1. Purpose

This document defines the mathematical properties relevant to Projection-Aware Certified Counterfactual Search (PAC-CF).

It distinguishes:

- Properties established by mathematical definitions.
- Properties that hold under stated assumptions.
- Properties that require implementation-level verification.
- Empirical observations that must not be presented as universal theorems.

The distinction is important: a property of an abstract projection does not automatically prove that every implementation satisfies it.

## 2. Notation

Let the historical temporal transaction graph be:

\[
G_t=(V,E_t,X,A)
\]

Where:

- \(V\) is the set of account nodes.
- \(E_t\) is the set of transaction edges available by target time \(t\).
- \(X\) contains node features.
- \(A\) contains edge attributes.

Let the frozen temporal graph predictor be:

\[
f_\theta(G_t,e_q)
\]

Where \(e_q\) is the target transaction and \(\theta\) denotes fixed model parameters.

Let the model score be:

\[
p=f_\theta(G_t,e_q)
\]

Let \(\tau\) be the configured decision threshold.

A target is on the risky side when:

\[
p\geq\tau
\]

A counterfactual is on the safe side when:

\[
p_{\mathrm{cf}}<\tau
\]

The threshold is an experimental decision parameter. It is not necessarily a calibrated fraud probability.

## 3. Intervention representation

Suppose the candidate historical transaction set is:

\[
E_c=\{e_1,e_2,\ldots,e_K\}
\]

Represent an intervention using a binary retention mask:

\[
M=(M_1,M_2,\ldots,M_K)
\]

Where:

\[
M_i=
\begin{cases}
1 & \text{if edge }e_i\text{ is retained}\\
0 & \text{if edge }e_i\text{ is removed}
\end{cases}
\]

The resulting graph is:

\[
G(M)=G_t\setminus
\{e_i\in E_c:M_i=0\}
\]

The intervention cardinality is:

\[
c(M)=\sum_{i=1}^{K}(1-M_i)
\]

This counts the number of removed candidate transactions.

The optimization objective prioritizes smaller intervention cardinality.

## 4. Counterfactual feasibility

A valid counterfactual must satisfy three separate conditions.

### 4.1 Model decision condition

\[
f_\theta(G(M),e_q)<\tau
\]

### 4.2 Temporal condition

Every retained transaction must belong to the permitted historical context.

No future transaction may be introduced to fund an earlier transaction.

### 4.3 WFFC condition

The intervention must satisfy the declared Weak Factual Flow Consistency constraints.

WFFC is a partial-observability flow model. It does not represent an independently verified account ledger.

Therefore, mathematical feasibility under WFFC must not be interpreted as proof of actual financial feasibility in a bank's internal records.

## 5. WFFC flow model

Let the chronological candidate transactions be indexed by \(k\).

For transaction \(k\), define:

- \(s_k\): source account.
- \(d_k\): destination account.
- \(a_k\): transaction amount.
- \(t_k\): transaction time.
- \(M_k\): retained transaction fraction.

For binary interventions, \(M_k\in\{0,1\}\). During relaxed optimization, \(M_k\in[0,1]\).

Let \(B_v(t)\) represent the modeled available flow for account \(v\) at time \(t\).

For an outgoing transaction, the retained amount is constrained by available modeled flow.

A projection may use:

\[
M'_k=
\min\left(
M_k,
\operatorname{clamp}
\left(
\frac{\max(0,B_{s_k})}{a_k},
0,1
\right)
\right)
\]

The corresponding modeled flow update is:

\[
B'_{s_k}=B_{s_k}-M'_ka_k
\]

\[
B'_{d_k}=B_{d_k}+M'_ka_k
\]

This is a simplified representation of the declared flow-consistency procedure.

The implementation must define how zero or invalid transaction amounts are handled. Division by zero must not be allowed.

### 5.1 Initial balances

The underlying dataset does not provide complete starting account balances.

Initializing every account with zero can make otherwise plausible historical flows infeasible.

The adopted relative causal-flow approach initializes root balances using the maximum deficit observed while processing the factual graph.

For account \(v\), define its cumulative net flow before transaction \(k\):

\[
C_{v,k}
=
\sum_{j<k:d_j=v}a_j
-
\sum_{j<k:s_j=v}a_j
\]

A possible maximum-deficit initialization is:

\[
B_v^0=
\max\left(0,-\min_k C_{v,k}\right)
\]

The initial balance is computed from the factual graph and then frozen during intervention evaluation.

It must not be recomputed independently for each proposed mask, because doing so would allow the intervention to alter its own initial conditions.

This initialization is a modeling convention for incomplete financial observations, not evidence that the account actually held that balance.

### 5.2 Temporal ordering

Transactions must be processed chronologically.

For equal timestamps, the implementation uses a deterministic tie-breaking order:

\[
(t_k,s_k,d_k,a_k,\text{original index})
\]

This provides deterministic processing but does not establish the true within-timestamp ordering of real-world bank events.

## 6. Projection properties

The following properties describe desirable behavior of the WFFC projection under the stated mathematical assumptions.

Let \(P\) denote the feasibility projection.

### 6.1 Feasibility

A projection is feasible when:

\[
P(M)\in\mathcal{F}
\]

Where \(\mathcal{F}\) is the set of masks satisfying the declared WFFC constraints.

This property requires the projection to process the complete relevant transaction sequence and correctly enforce the constraints.

It must also be independently checked by a validator.

### 6.2 Deflationary behavior

For a retention-mask projection, the output should not retain more of an edge than the input:

\[
P(M)_i\leq M_i
\]

This means projection may retain the same or a smaller fraction of a transaction, but it cannot increase the proposed retained fraction.

### 6.3 Idempotence

An idempotent projection satisfies:

\[
P(P(M))=P(M)
\]

Once a mask has been projected, applying the same projection again should not change it.

This property depends on deterministic processing, fixed initial conditions, stable event ordering, and a consistent implementation of the constraints.

### 6.4 Monotonicity

For two masks \(M_1\) and \(M_2\), define component-wise ordering:

\[
M_1\leq M_2
\]

A monotone projection satisfies:

\[
P(M_1)\leq P(M_2)
\]

Under the intended flow model, retaining fewer incoming transactions should not create more available flow for downstream outgoing transactions.

This property requires fixed initial balances and consistent chronological processing. It must not be assumed for a different projection implementation without proof or testing.

### 6.5 Determinism

For identical inputs and configuration:

\[
P(M)=P(M)
\]

The same candidate sequence, initial conditions, mask, and implementation version should produce the same output.

This is an implementation requirement, not a consequence of the notation alone.

## 7. Model-score consistency

Let the final returned mask be \(M_f\).

The returned score must satisfy:

\[
s_f=f_\theta(G(M_f),e_q)
\]

This is the score-mask consistency invariant.

If projection transforms a proposal \(M_p\) into \(M_f\), then:

\[
M_f=P(M_p)
\]

The score for \(M_p\) cannot be reused as the score for \(M_f\), unless the masks are identical.

This requirement is particularly important because TGAT predictions are not assumed to be monotonic under edge removal.

A valid counterfactual therefore requires an exact evaluation of the final mask.

## 8. Minimality and certification

Define the feasible counterfactual set:

\[
\mathcal{C}=
\left\{
M:
P(M)=M,\;
f_\theta(G(M),e_q)<\tau
\right\}
\]

The minimum-cardinality objective is:

\[
M^*
\in
\arg\min_{M\in\mathcal{C}}c(M)
\]

Solving this objective over the entire intervention space may be computationally expensive.

PAC-CF therefore uses bounded exact search.

### 8.1 Bounded certification

Suppose the exact search exhaustively checks every candidate intervention of cardinality smaller than \(r\), and finds a valid intervention of cardinality \(r\).

Then no feasible intervention with cardinality less than \(r\) exists within the checked candidate space.

The result is minimum-cardinality within that declared search space.

### 8.2 What certification does not prove

A bounded certificate does not establish:

- Minimum cardinality over all possible transactions outside the candidate window.
- Minimum cardinality under a different feasibility model.
- Minimum cardinality under a different TGAT checkpoint.
- Minimum cardinality under a different threshold.
- Minimum cardinality under an expanded search space.
- Global optimality of a heuristic fallback result.

The term `CERTIFIED_GLOBAL_MINIMUM` is an implementation status label and must always be interpreted with its declared search-space boundary.

## 9. Search complexity

For \(K\) candidate edges and maximum exact cardinality \(d\), exhaustive subset search evaluates:

\[
N(K,d)=\sum_{i=1}^{d}\binom{K}{i}
\]

For the documented V3 configuration:

\[
d=3
\]

Therefore:

\[
N(K,3)=
K+\frac{K(K-1)}{2}
+\frac{K(K-1)(K-2)}{6}
\]

The number of evaluations grows rapidly as \(K\) increases.

The bounded heuristic stage is used to control additional search cost when exact search does not establish a certified solution.

## 10. Lexicographic objective

PAC-CF prioritizes intervention cardinality before secondary criteria.

A lexicographic objective can be represented as:

\[
\min_M
\left(
c(M),
-\operatorname{margin}(M),
\operatorname{tie}(M)
\right)
\]

Where:

- \(c(M)\) is the number of removed transactions.
- \(\operatorname{margin}(M)\) measures the distance below the decision threshold.
- \(\operatorname{tie}(M)\) is a deterministic tie-breaking criterion.

The precise margin definition and tie-breaking implementation must be recorded in the experiment configuration.

The secondary criteria must not override the primary minimum-cardinality objective.

## 11. Non-monotonic predictor behavior

No general property guarantees:

\[
f_\theta(G(M_1),e_q)
\leq
f_\theta(G(M_2),e_q)
\]

whenever \(M_1\leq M_2\).

Removing an edge can change temporal neighborhoods, attention weights, and learned representations.

Consequently:

- A larger intervention may increase the score.
- A smaller intervention may produce a safer decision.
- A proposal's score may not predict its projected score.
- Greedy search can miss valid combinations.
- Every returned intervention must be evaluated directly.

PAC-CF does not rely on score monotonicity for its certification logic.

## 12. Empirical validation requirements

Mathematical claims must be supported by appropriate tests.

| Property | Required validation |
|---|---|
| Feasibility | Independent WFFC validator |
| Deflationary behavior | Component-wise mask comparison |
| Idempotence | Compare \(P(P(M))\) with \(P(M)\) |
| Monotonicity | Compare projected masks for ordered input masks |
| Determinism | Repeated execution with identical inputs |
| Score consistency | Re-evaluate the exact returned mask |
| Minimality | Exhaustive evaluation of all smaller cardinalities |
| Temporal validity | Independent chronological-flow validation |
| Cache integrity | Verify cache key covers all score-relevant inputs |

Passing synthetic tests supports correctness for the tested cases. It does not automatically establish universal correctness for all possible temporal graphs.

## 13. Claims and evidence boundaries

The following language should be used when reporting PAC-CF properties.

| Claim | Appropriate interpretation |
|---|---|
| WFFC projection is idempotent | Under the documented fixed-input and implementation assumptions, with supporting tests |
| WFFC projection is monotone | Under the declared flow model, with mathematical and implementation validation |
| Final score is consistent | The reported score is evaluated for the exact returned mask |
| Counterfactual is feasible | It passes the specified WFFC validator |
| Counterfactual is minimal | Only within the exhaustively checked candidate space |
| Search is deterministic | Repeated runs agree under identical data, seed, and configuration |
| Method is globally optimal | Do not claim without unrestricted proof over the relevant intervention space |

## 14. Limitations

The mathematical framework has several boundaries.

1. WFFC is not a real bank-balance reconstruction.
2. Initial balances are inferred from observed flow deficits rather than supplied by a bank ledger.
3. Same-timestamp tie-breaking is deterministic but may not reflect actual transaction order.
4. TGAT score changes do not establish real-world causal effects.
5. Exact certification applies only to the declared finite candidate space.
6. Synthetic graph tests cannot establish performance on all real transaction systems.
7. Theoretical projection properties require implementation assumptions that must be checked against the actual code.

## 15. Conclusion

PAC-CF separates proposal generation, feasibility projection, exact model evaluation, and bounded discrete search.

Its central mathematical and engineering requirement is that feasibility, score, and certification all refer to the same final intervention.

Claims of feasibility, determinism, monotonicity, idempotence, and minimality must remain tied to their respective assumptions and validation evidence.
