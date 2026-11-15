# PAC-CF: Projection-Aware Certified Counterfactual Search

### Constrained Counterfactual Explanations for Temporal Graph Neural Networks in Anti-Money Laundering

**Research project | Temporal Graph Neural Networks | Explainable AI | Financial Crime Detection**

---

## 1. Overview

PAC-CF (Projection-Aware Certified Counterfactual Search) is a research framework designed to generate constrained counterfactual explanations for predictions made by Temporal Graph Neural Networks (TGNNs).

The research focuses on Anti-Money Laundering (AML), where financial transactions form evolving temporal graphs and suspicious activity may involve complex relationships between multiple accounts.

While temporal graph models can identify potentially suspicious transactions, understanding why a model produces a particular prediction remains challenging.

PAC-CF investigates whether a small set of historical transaction edges can be identified whose removal changes the model's prediction while satisfying defined temporal and financial-flow constraints.

The framework combines:

- Candidate intervention proposal.
- Temporal and financial feasibility projection.
- Exact evaluation using a frozen temporal graph predictor.
- Bounded discrete counterfactual search.
- Minimum-cardinality certification within an explicitly defined search space.

The central objective is to make counterfactual explanations more constrained, reproducible, and technically defensible.

## 2. Research Motivation

Financial transactions are naturally represented as temporal graphs:

- Accounts represent nodes.
- Transactions represent directed edges.
- Transaction amounts and metadata represent edge attributes.
- Transaction timestamps represent temporal information.

A suspicious transaction may not appear unusual in isolation. Its significance may depend on earlier transfers, account relationships, and the sequence of financial activity.

Temporal Graph Neural Networks can capture these relationships, but their predictions are not automatically interpretable.

A counterfactual explanation attempts to answer:

**What is the smallest feasible change to the historical transaction graph that would change the model's prediction?**

However, unconstrained counterfactual methods can produce unrealistic interventions. For example, they may remove transactions without considering chronological order or whether the resulting financial-flow sequence satisfies a defined feasibility model.

PAC-CF was developed to investigate these limitations.

## 3. Core Research Contributions

The research investigates the following components.

### 3.1 Weak Factual Flow Consistency (WFFC)

WFFC is a simplified financial-flow feasibility model designed for transaction graphs with incomplete opening-balance information.

It processes transactions chronologically and applies a deterministic relative initialization to evaluate whether retained transaction flows satisfy the defined constraints.

WFFC is not a real bank ledger and does not reconstruct verified account balances. It represents a deliberately limited feasibility model.

### 3.2 Projection-Aware Counterfactual Generation

PAC-CF applies a feasibility projection to candidate interventions.

If projection changes a proposed intervention, the resulting final intervention must be evaluated again using the original frozen predictor.

### 3.3 Exact Final-Mask Evaluation

A central design invariant is:

The reported counterfactual score must correspond to the exact final intervention returned by the algorithm.

This prevents a stale prediction score from being associated with a different intervention after feasibility projection.

### 3.4 Bounded Discrete Search and Certification

PAC-CF investigates discrete intervention search in increasing cardinality.

A minimum-cardinality certificate is issued only when all feasible interventions of smaller cardinality in the declared finite search space have been exhaustively checked.

This is a bounded certification claim, not unrestricted global optimality.

## 4. Research Architecture

The documented PAC-CF V3 workflow consists of four principal stages:

1. **Proposal:** Generate and rank candidate edge-removal interventions.
2. **Projection:** Apply WFFC to enforce the declared feasibility constraints.
3. **Exact Evaluation:** Evaluate the final projected intervention using the frozen TGAT predictor.
4. **Certification:** Perform bounded discrete search and issue a certificate only when the required exhaustive checks have been completed.

The proposal stage may use continuous optimization and gradients to identify promising candidates. These mechanisms do not replace exact discrete evaluation.

## 5. Dataset and Predictor

The research used the **IBM AMLSim HI-Small** synthetic financial transaction dataset.

The primary temporal graph predictor was based on the Temporal Graph Attention Network (TGAT).

The documented optimized A4 architecture used:

| Parameter | Configuration |
|---|---:|
| Input channels | 16 |
| Edge features | 39 |
| Hidden dimension | 64 |
| Time dimension | 64 |
| Attention heads | 4 |
| Temporal field | `rel_minute` |

The documented edge representation included transaction amounts, receiving currency, payment currency, and payment format features.

The original A4 and A5 checkpoints were subsequently lost. A later A4-equivalent reconstruction was reported, but it must not be confused with the original A4 checkpoint.

See [Predictor and Dataset](docs/model-and-data.md).

## 6. Historical Experimental Results

The preserved project record designates a 479-target PAC-CF V3 primary evaluation.

| Outcome | Historical report |
|---|---:|
| Successful counterfactuals | 473 / 479 |
| Counterfactual success rate | 98.75% |
| No feasible result found within search | 5 |
| Already-safe targets | 1 |
| Certified minimum-cardinality results | 471 |
| Exact-search successes without certificate | 23 |

For the 473 successful counterfactuals, the historical record reports:

- WFFC validity: 473/473.
- Execution-path score/mask consistency: 473/473.

These are historical reported results. The original raw experiment outputs, source code, checkpoints, and validation artifacts are not included in this repository and have not been independently reproduced here.

A separate 500-target artifact was also reported. It is documented separately and must not be merged with the 479-target primary evaluation.

See [Historical Experimental Results](docs/experiments/reported-results.md).

## 7. Baseline Research

The research also investigated two temporal graph counterfactual explanation methods:

- **CoDy:** Counterfactual Explainers for Dynamic Graphs.
- **TemGX:** Training-Free Counterfactual Explanation for Temporal Graph Model Inference.

The intended comparison preserved the native baseline algorithms and evaluated their generated interventions under the research's validation criteria.

Baseline results require careful interpretation because intervention definitions and feasibility assumptions may differ.

No claim of statistically significant superiority or state-of-the-art performance is made in this archive.

## 8. Development History

The project evolved through several stages:

- TGAT architecture optimization and A4/A5 experiments.
- B0–B7 application architecture and implementation work.
- Early Counterfactual Combinatorial Optimization (CCO).
- WFFC feasibility modelling and validation.
- Identification of stale-score and discrete-mask defects in earlier approaches.
- Development of the PAC-CF V3 design.

The B0–B7 work also documented a FastAPI backend, React/Vite investigation dashboard, Integrated Gradients explanations, temporal graph context, and end-to-end validation.

The original application source code is not currently available.

See [Development History](docs/development-history.md).

## 9. Repository Status and Provenance

**Important: This repository is a reconstructed research documentation archive.**

The original workstation SSD failed, resulting in the loss of the original project files.

The documentation is being reconstructed from preserved research discussions and historical project records.

Consequently:

- Original source code is not included.
- Original A4/A5 checkpoints are not included.
- Original raw evaluation outputs are not included.
- Historical metrics are not independently reproduced by this repository.
- Recorded hashes are historical references and have not been reverified here.
- Any future software implementation must be identified as a clean-room reconstruction unless original source artifacts are recovered.

The repository distinguishes historical reports, reconstructed documentation, mathematical/design descriptions, and independently verified evidence.

See [Provenance and Evidence Register](docs/provenance-and-evidence.md).

## 10. Limitations

The following limitations are important:

1. AMLSim HI-Small is synthetic data and does not establish real-world banking performance.
2. A sigmoid-transformed model logit is not automatically a calibrated fraud probability.
3. Integrated Gradients measures model sensitivity relative to a baseline; it does not prove causation.
4. WFFC is a simplified feasibility model, not a true bank ledger.
5. Counterfactual explanations describe model behaviour under specified interventions, not real-world causal effects.
6. Minimum-cardinality certificates apply only to the finite search space actually examined.
7. A successful counterfactual does not establish innocence, guilt, intent, or criminality.

PAC-CF is a research explanation framework, not an autonomous financial enforcement system.

## 11. Repository Structure

The planned documentation structure is:

```text
PAC-CF-Research/
│
├── README.md
├── .gitignore
├── CONTRIBUTING.md
├── LICENSE-NOTICE.md
├── REFERENCES.md
│
├── docs/
│   ├── problem-formulation.md
│   ├── algorithm.md
│   ├── wffc-specification.md
│   ├── model-and-data.md
│   ├── evaluation-protocol.md
│   ├── development-history.md
│   ├── limitations-and-ethics.md
│   ├── provenance-and-evidence.md
│   ├── reproducibility-plan.md
│   │
│   └── experiments/
│       └── reported-results.md
│
└── paper/
    └── PAC-CF-Research-Paper.md
```

Additional technical documents, diagrams, and reproducibility artifacts may be added as they become available.

## 12. Research Objective Going Forward

The next stage is to recover or reconstruct the original research implementation and independently validate the documented methodology.

Priority areas include:

- Formal specification and proof review of WFFC.
- Independent implementation and validation of PAC-CF.
- TGAT predictor reconstruction and parity testing.
- Reproduction of the primary evaluation.
- Harmonized CoDy and TemGX benchmarking.
- Ablation studies and statistical analysis.
- Reproducibility and artifact preservation.

The long-term objective is to develop a technically rigorous, independently reproducible framework for constrained counterfactual explanations of temporal graph models.

---

## References

See [REFERENCES.md](REFERENCES.md) for the research bibliography and related work.

---

**PAC-CF Research Project**

*Projection-Aware Certified Counterfactual Search for Temporal Graph Neural Networks in Anti-Money Laundering.*
