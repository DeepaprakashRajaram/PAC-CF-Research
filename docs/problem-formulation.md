# Problem Formulation

## 1. Overview

Projection-Aware Certified Counterfactual Search (PAC-CF) addresses the problem of finding small, temporally valid, and financially feasible counterfactual explanations for predictions made by temporal graph neural networks.

The research focuses on anti-money-laundering (AML) transaction graphs, where transactions are represented as time-stamped directed edges between accounts.

A temporal graph model can assign a high-risk score to a transaction using information from its historical graph context. However, the score alone does not explain which historical transactions are relevant to the model's decision.

PAC-CF searches for a constrained intervention on historical transactions that changes the frozen model's prediction.

The objective is not to determine whether an account or individual has committed a crime. It is to identify a model-level counterfactual under explicitly documented assumptions.

## 2. Temporal transaction graph

Let the transaction history up to time \(t\) be represented by:

\[
G_t=(V,E_t,X,A)
\]

where:

- \(V\) is the set of account nodes.
- \(E_t\) is the set of transactions available at or before time \(t\).
- \(X\) represents node features.
- \(A\) represents transaction or edge attributes.

Each transaction edge is represented as:

\[
e_k=(u_k,v_k,a_k,\tau_k,\mathbf{x}_k)
\]

where:

- \(u_k\) is the source account.
- \(v_k\) is the destination account.
- \(a_k\) is the transaction amount.
- \(\tau_k\) is the transaction time.
- \(\mathbf{x}_k\) contains the transaction features.

The target transaction is evaluated against its permitted historical context.

Future transactions must not contribute to an explanation of an earlier target transaction.

## 3. Frozen prediction model

Let the trained temporal graph predictor be:

\[
f_\theta(G_t,e_q)
\]

where:

- \(\theta\) denotes the trained model parameters.
- \(G_t\) is the permitted historical graph context.
- \(e_q\) is the target transaction.

The model produces a scalar logit:

\[
z_q=f_\theta(G_t,e_q)
\]

A sigmoid transformation may be applied:

\[
s_q=\sigma(z_q)=\frac{1}{1+e^{-z_q}}
\]

The transformed score is a monotonic transformation of the model logit. It must not be interpreted as a calibrated probability unless calibration has been independently established.

For a specified threshold \(\tau\), the model decision is:

\[
\hat{y}_q=
\begin{cases}
1 & s_q\geq\tau\\
0 & s_q<\tau
\end{cases}
\]

The threshold is an experimental decision boundary, not a universal AML risk boundary.

During counterfactual search, the trained predictor is frozen. PAC-CF changes the graph intervention, not the learned model parameters.

## 4. Counterfactual intervention

Let \(S\subseteq E_t\) be a set of historical transactions selected for removal.

The resulting counterfactual graph is:

\[
G'_t=G_t\setminus S
\]

The counterfactual score is:

\[
s'_q=\sigma(f_\theta(G'_t,e_q))
\]

For an initially high-scoring target, a successful counterfactual must satisfy:

\[
s_q\geq\tau
\]

and:

\[
s'_q<\tau
\]

The intervention therefore changes the frozen model's decision under the specified graph and feasibility rules.

A counterfactual is not considered valid merely because an optimizer proposes a mask. The final intervention must be projected, evaluated, and checked.

## 5. Constrained optimization objective

PAC-CF seeks a small intervention that changes the prediction while satisfying the documented feasibility constraints.

A simplified formulation is:

\[
\min_{S\subseteq E_t}|S|
\]

subject to:

\[
s'_q<\tau
\]

\[
\operatorname{WFFC}(G'_t)=\text{valid}
\]

\[
\operatorname{Temporal}(G'_t)=\text{valid}
\]

where:

- \(|S|\) is the number of removed historical transactions.
- WFFC is the Weak Factual Flow Consistency constraint.
- Temporal validity enforces the documented causal ordering rules.

The formulation is intentionally constrained. It does not claim that every transaction deletion is physically possible in a real financial institution.

## 6. Binary intervention representation

For each candidate historical edge \(e_k\), define a binary mask:

\[
M_k\in\{0,1\}
\]

where:

- \(M_k=1\): retain the edge.
- \(M_k=0\): remove the edge.

The intervention graph is:

\[
G_t(\mathbf{M})=
(V,\{e_k\in E_t:M_k=1\},X,A)
\]

The final score must be evaluated using the exact final mask:

\[
s_{\mathrm{final}}=
\sigma(f_\theta(G_t(\mathbf{M}_{\mathrm{final}}),e_q))
\]

This identity is a central correctness requirement. A score calculated for an earlier proposal must never be reported as the score of a different projected or modified mask.

## 7. Why projection is necessary

A model-level edge deletion can violate temporal or financial-flow assumptions.

For example, deleting a transaction that supplies funds to a later transaction may make the later transaction infeasible under the project's flow model.

Consequently, the search procedure must not rely solely on a neural optimizer's proposed mask.

PAC-CF introduces a feasibility projection stage that converts a proposed intervention into a mask satisfying the implemented constraints.

The projected mask is then evaluated by the frozen predictor.

## 8. Bounded certification

PAC-CF includes exact discrete search for small intervention cardinalities.

For a candidate history containing \(K\) edges, an exhaustive search over all subsets of cardinality up to \(d\) has a search-space size:

\[
N(K,d)=\sum_{i=1}^{d}\binom{K}{i}
\]

If a valid intervention of cardinality \(d\) is found after every feasible intervention of smaller cardinality has been exhaustively checked, the result can be certified as minimum-cardinality within the declared search space.

This is a bounded guarantee.

It does not establish unrestricted global optimality over every possible intervention, every graph representation, or every real-world financial interpretation.

## 9. Research questions

The research investigates the following questions:

1. Can a projection-aware search procedure produce counterfactual interventions that satisfy the implemented temporal and flow constraints?
2. Can the final returned intervention be evaluated exactly by the frozen temporal graph predictor?
3. Can exhaustive discrete search certify minimum intervention cardinality within a bounded search space?
4. How does PAC-CF compare with existing temporal graph counterfactual methods under a common evaluation protocol?
5. What are the computational costs and practical limitations of constrained counterfactual search?
6. How sensitive are the findings to the chosen feasibility assumptions and evaluation population?

## 10. Scope

The demonstrated research context is synthetic AML temporal transaction graphs using IBM AMLSim HI-Small.

The work does not establish:

- Real-world bank-ledger feasibility.
- Actual customer balances.
- Criminal intent.
- Confirmed money laundering.
- Causal responsibility.
- Production-grade AML effectiveness.
- Universal optimality outside the declared bounded search procedure.

PAC-CF is a research method for explaining the behavior of a frozen temporal graph predictor under explicit constraints.
