# WFFC Specification

## 1. Purpose

WFFC stands for **Weak Factual Flow Consistency**.

It is a feasibility model used by PAC-CF to constrain counterfactual transaction-edge deletions.

The model represents a limited form of temporal financial-flow consistency under partial observability.

It is not a complete financial ledger, a bank-account balance reconstruction, or proof that a transaction was financially possible in the real world.

## 2. Motivation

A transaction graph contains directed transfers between accounts.

If a historical transaction supplies funds to an account, a later outgoing transaction may depend on those funds.

Removing an incoming transaction can therefore affect the feasibility of later outgoing transactions.

A counterfactual method that deletes edges independently may produce an intervention that violates this dependency.

WFFC introduces a deterministic flow projection to address this problem under an explicitly limited balance model.

## 3. Transaction representation

Represent a transaction as:

\[
e_k=(u_k,v_k,a_k,t_k)
\]

where:

- \(u_k\): source account.
- \(v_k\): destination account.
- \(a_k\): transaction amount.
- \(t_k\): transaction time.

Each edge has a retention mask:

\[
M_k\in[0,1]
\]

Interpretation:

- \(M_k=1\): fully retained.
- \(M_k=0\): fully removed.
- \(0<M_k<1\): partially retained in the relaxed flow representation.

The final PAC-CF intervention is binary. Fractional masks are used during projection or proposal stages, not as a substitute for the final binary intervention.

## 4. Flow balance

Let \(B_v(t)\) represent the modeled available flow balance for account \(v\) at time \(t\).

For a transaction from \(u_k\) to \(v_k\), the retained flow is constrained by the modeled available balance at the source.

A basic projection rule is:

\[
M'_k=
\min\left(
M_k,
\operatorname{clamp}
\left(
\frac{\max(0,B_{u_k})}{a_k},
0,
1
\right)
\right)
\]

for positive transaction amounts.

The retained transaction amount is:

\[
a'_k=M'_k a_k
\]

The modeled source balance is reduced by the retained outgoing flow, and the destination balance is increased by the same amount:

\[
B_{u_k}\leftarrow B_{u_k}-a'_k
\]

\[
B_{v_k}\leftarrow B_{v_k}+a'_k
\]

This is a simplified flow model. It does not include all real-world banking mechanisms.

## 5. Initial-balance problem

The dataset pipeline does not provide complete account starting balances.

A strict initialization of:

\[
B_v(0)=0
\]

for every account can make the first outgoing transaction infeasible, even when the factual transaction sequence contains incoming and outgoing transfers.

This creates an initialization problem: the flow model would reject factual transactions because it lacks information about funds that may have existed before the observed history.

The initial zero-balance approach was therefore identified as unsuitable for the intended partial-observability setting.

## 6. Relative Causal Flow initialization

The adopted approach uses a relative causal-flow initialization based on the maximum deficit observed in the factual transaction sequence.

For an account, the initialization estimates the minimum starting balance needed to make its factual outgoing and incoming flow sequence feasible under the simplified model.

A simplified account-level expression is:

\[
B_v(0)=
\max\left(
0,
\max_j
\left[
\sum_{k\leq j,\ u_k=v}a_k
-
\sum_{k\leq j,\ v_k=v}a_k
\right]
\right)
\]

where the transactions are considered in the specified chronological order.

This expression is an explanatory representation of the maximum-deficit principle. The exact implementation must be consulted for details of event ordering, amount normalization, and tie-breaking.

The initialization is calculated once from the factual graph and then frozen for counterfactual evaluation.

It must not be recomputed separately for each candidate mask.

Recomputing initial balances after deleting edges could allow the counterfactual to create new starting funds to compensate for its own deletions.

## 7. Chronological processing

Transactions must be processed deterministically.

The documented ordering key is:

\[
(t_k,u_k,v_k,a_k,i_k)
\]

where \(i_k\) is the original transaction index used as a final tie-breaker.

This ensures deterministic handling of transactions with equal timestamps and otherwise identical attributes.

The exact implementation must preserve the original index when constructing the ordered event sequence.

## 8. Causal flow propagation

When a transaction is removed or partially retained, its downstream funding contribution changes.

The projection must propagate this change through subsequent transactions.

For example:

- Account A transfers funds to B.
- Account B later transfers funds to C.
- If the A-to-B transfer is removed, B may no longer have sufficient modeled flow to support the B-to-C transfer.
- The later transfer may therefore need to be reduced or removed under WFFC.

This is a causal flow dependency within the modeled transaction sequence.

It is not evidence of actual account balances or proof that the downstream transaction was impossible in a real banking system.

## 9. Candidate history

The candidate intervention window consists of the permitted historical edges for the target transaction, excluding the target transaction itself.

The target transaction is not treated as an ordinary removable historical edge.

The temporal context must remain fixed for the entire search.

No future edge may be introduced to fund an earlier transaction.

## 10. Projection properties

The research implementation and synthetic tests examine the following properties.

### 10.1 Factual-mask identity

The all-retained factual mask should remain unchanged when the factual graph is feasible under the frozen initialization.

### 10.2 Determinism

The same factual graph, mask, configuration, and ordering must produce the same projected result.

### 10.3 Idempotence

Applying the projection to an already projected result should not change it:

\[
P(P(M))=P(M)
\]

This property is evaluated under the implementation's projection assumptions.

### 10.4 Monotonicity

The implementation tests whether reducing the retained flow proposal can cause the projection to retain more flow under the same frozen initialization and event ordering.

The exact scope of this property depends on the projection implementation.

### 10.5 Independent validation

A separate validation procedure checks the projected result against the WFFC constraints.

The validator should not simply trust the projection's own success flag.

## 11. Synthetic validation

The WFFC implementation was tested using deterministic synthetic cases and an independent exhaustive pure-Python oracle.

The documented test areas include:

- Factual-mask identity.
- Initial-balance minimality.
- Causal starvation.
- Multi-hop cascade behavior.
- Chronological interleaving.
- Deterministic tie-breaking.
- Projection idempotence.
- Monotonicity checks.
- Gradient-flow behavior for relaxed masks.
- Production projection agreement with oracle cases.

A reported synthetic test suite contained 14 passing tests. A later PAC-CF invariant suite contained 20 synthetic invariants.

These are implementation-level tests, not proof that WFFC represents actual bank balances or every possible financial-flow scenario.

## 12. Known limitations

WFFC does not model:

- Complete real account balances.
- Cash deposits not represented in the observed graph.
- Withdrawals or transfers outside the dataset.
- Credit facilities or overdrafts.
- Fees, reversals, chargebacks, or settlement delays.
- Currency conversion and exchange-rate effects.
- Multiple financial institutions.
- Legal ownership or beneficial ownership.
- All forms of partial observability.

The model's initial balance is an inferred feasibility allowance, not an observed account balance.

## 13. Interpretation

A WFFC-valid intervention is valid under the project's implemented weak flow-consistency model.

It must not be described as:

- Proven financially possible in a real bank.
- Verified against a bank ledger.
- A reconstruction of actual account balances.
- Proof of suspicious intent.
- Proof that a transaction occurred or did not occur.

The correct interpretation is:

**The intervention satisfies the documented WFFC flow constraints under the specified partial-observability assumptions.**
