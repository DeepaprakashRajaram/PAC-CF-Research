# References

This document records the principal research papers, datasets, frameworks, and tools relevant to PAC-CF.

References are provided to help readers locate the original material. A citation does not imply that the cited work uses the same problem formulation, constraints, evaluation protocol, or guarantees as PAC-CF.

Bibliographic details and current versions should be checked against the original publication or project before formal academic submission.

## 1. Temporal graph learning

### 1.1 Temporal Graph Attention Networks (TGAT)

Xu, D., Ruan, C., Korpeoglu, E., Kumar, S., and Achan, K. (2020).

**Inductive Representation Learning on Temporal Graphs.**

International Conference on Learning Representations (ICLR).

arXiv:2002.07962

https://arxiv.org/abs/2002.07962

Relevance:
- Introduces temporal graph attention for inductive representation learning.
- Provides the temporal graph neural network foundation used by the PAC-CF research pipeline.
- Relevant to temporal neighborhood aggregation and time-aware graph representations.

## 2. Counterfactual explanations for temporal graphs

### 2.1 CoDy

**CoDy: Counterfactual Explainers for Dynamic Graphs.**

Proceedings of the International Conference on Machine Learning (ICML), Proceedings of Machine Learning Research, 2025.

PMLR, Volume 267, pages 50762–50785.

Relevance:
- A counterfactual explanation method for dynamic graph models.
- Uses a search procedure involving Monte Carlo Tree Search (MCTS) and heuristics.
- Serves as a relevant comparison method for temporal graph counterfactual research.

Official publication source:

https://proceedings.mlr.press/

### 2.2 TemGX

**Training-Free Counterfactual Explanation for Temporal Graph Model Inference.**

International Conference on Learning Representations (ICLR), 2026.

Official publication source:

https://openreview.net/

Relevance:
- A training-free counterfactual explanation approach for temporal graph model inference.
- Relevant to temporal influence, time-aware reasoning, and search-based counterfactual generation.
- Considered as a comparison method in the PAC-CF research.

The exact paper record and version should be checked on OpenReview before final academic citation.

## 3. Anti-money-laundering simulation

### 3.1 IBM AMLSim

IBM.

**AMLSim: Anti-Money Laundering Simulator.**

Official project repository:

https://github.com/IBM/AMLSim

Relevance:
- Provides synthetic financial transaction data and simulation capabilities.
- Used as the dataset-generation and experimental context for the AML research.
- Supports experiments involving transaction patterns and simulated suspicious activity.

Important qualification:

AMLSim produces synthetic data. Results obtained using it should not be presented as validation on real bank transactions or real-world confirmed financial crime.

## 4. Explainability methods

### 4.1 Integrated Gradients

Sundararajan, M., Taly, A., and Yan, Q. (2017).

**Axiomatic Attribution for Deep Networks.**

Proceedings of the 34th International Conference on Machine Learning (ICML).

Proceedings of Machine Learning Research, Volume 70, pages 3319–3328.

https://proceedings.mlr.press/v70/sundararajan17a.html

Relevance:
- Introduces Integrated Gradients.
- Provides the attribution-method foundation used in the project's feature-attribution work.
- Attribution values describe contributions relative to a chosen baseline; they do not independently establish causality.

## 5. Software and implementation references

The following software may be relevant to the implementation and reproduction of the research:

| Software | Purpose | Official source |
|---|---|---|
| Python | Research implementation language | https://www.python.org/ |
| PyTorch | Neural network implementation | https://pytorch.org/ |
| PyTorch Geometric | Graph learning infrastructure | https://pytorch-geometric.readthedocs.io/ |
| NumPy | Numerical computation | https://numpy.org/ |
| scikit-learn | Evaluation metrics and utilities | https://scikit-learn.org/ |
| FastAPI | Research application API | https://fastapi.tiangolo.com/ |
| React | Frontend interface | https://react.dev/ |
| Vite | Frontend development and build tooling | https://vite.dev/ |
| Cytoscape.js | Graph visualization | https://js.cytoscape.org/ |
| Playwright | Browser-based testing | https://playwright.dev/ |

The presence of a software package in this table does not establish that every listed version was used in every historical experiment. Exact environment versions must be taken from the corresponding experiment records.

## 6. Citation and evidence policy

When adding a reference to this repository:

1. Prefer the original publication or official project repository.
2. Include authors, title, venue, year, and DOI or stable URL when available.
3. Do not cite search-result snippets as primary references.
4. Distinguish peer-reviewed publications from preprints and project documentation.
5. Verify publication status and bibliographic details before using a reference in a paper.
6. Explain how the reference relates to PAC-CF rather than implying that it validates PAC-CF.

## 7. Research distinction

PAC-CF is a separate research formulation.

Its projection-aware workflow, WFFC feasibility model, exact final-mask evaluation, and bounded certification procedure should not be attributed to CoDy, TemGX, TGAT, or any other referenced work unless the original source explicitly supports that attribution.
