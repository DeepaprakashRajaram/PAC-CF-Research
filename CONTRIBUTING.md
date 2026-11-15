# Contributing to PAC-CF Research

Thank you for your interest in the Projection-Aware Certified Counterfactual Search (PAC-CF) research project.

This repository is intended to preserve the project's research design, technical development history, experimental methodology, reported findings, and reproducibility documentation.

Contributions are welcome, particularly those that improve clarity, reproducibility, scientific accuracy, and the quality of the research record.

## 1. Contribution principles

All contributions must follow these principles:

1. **Scientific integrity:** Do not fabricate results, measurements, experimental observations, or citations.
2. **Traceability:** Important claims must be linked to an identifiable source, experiment, implementation, or documented derivation.
3. **Reproducibility:** Distinguish independently reproduced results from previously reported results.
4. **Technical precision:** Use terminology consistently, especially for counterfactual validity, temporal constraints, feasibility, and certification.
5. **Transparency:** Document limitations, failed experiments, methodological changes, and unresolved questions.
6. **Research safety:** Do not publish private data, credentials, proprietary material, or artifacts without appropriate authorization.

## 2. Repository scope

Contributions may include:

- Corrections to research documentation.
- Improvements to mathematical definitions and explanations.
- Literature references and related-work analysis.
- Reproducibility instructions.
- Experimental protocol documentation.
- Clarifications to the PAC-CF search procedure.
- Improvements to the WFFC feasibility specification.
- Documentation of limitations and threats to validity.
- Verified figures, tables, and diagrams.
- Independently reproduced experimental evidence.

This repository does not treat an undocumented implementation or an unverified numerical claim as established research evidence.

## 3. Reporting issues

When reporting an issue, provide as much of the following information as possible:

- The affected file or section.
- A concise description of the problem.
- Why the existing explanation or claim is incorrect or incomplete.
- A proposed correction, if available.
- Supporting references, equations, logs, or experimental evidence.

For experimental issues, include the relevant configuration, dataset version, target population, evaluation conditions, and artifact identifiers where available.

Do not attach confidential datasets, account information, credentials, or personally identifiable information.

## 4. Documentation contributions

Documentation changes should:

- Preserve the distinction between the original research design and later revisions.
- Identify assumptions explicitly.
- Define symbols before using them in equations.
- Explain the scope of mathematical guarantees.
- Identify whether a result is theoretical, experimentally observed, or inferred.
- Preserve relevant negative findings and failed approaches.
- Avoid presenting synthetic AMLSim transactions as real financial-crime cases.

Use clear Markdown headings, readable equations, descriptive tables, and stable references.

## 5. Experimental evidence requirements

Any contribution that introduces or changes an experimental claim should document:

| Required item | Description |
|---|---|
| Dataset | Dataset name, version, and relevant preprocessing |
| Model | Architecture and checkpoint identity |
| Evaluation set | Target population and selection procedure |
| Configuration | Relevant hyperparameters and thresholds |
| Procedure | Exact evaluation or search procedure |
| Metrics | Definitions, denominators, and aggregation method |
| Evidence | Logs, reports, manifests, or reproducible artifacts |
| Limitations | Known restrictions and unresolved issues |

A reported result must not be described as independently reproduced unless the reproduction has actually been performed and documented.

A successful counterfactual search must not automatically be described as globally optimal. Any certification claim must state the exact search space and conditions under which the certification holds.

## 6. Terminology and claim discipline

Please preserve the following distinctions:

- **Model score:** The output of the frozen predictor. It is not automatically a calibrated fraud probability.
- **Counterfactual explanation:** An intervention intended to change a model's prediction under specified constraints. It does not establish what happened in the real world.
- **WFFC feasibility:** A weak factual-flow consistency model under partial balance observability. It is not a verified bank ledger or proof of actual available funds.
- **Temporal validity:** Compliance with the documented temporal ordering rules. It does not establish criminal intent.
- **Integrated Gradients:** A feature-attribution method. Attribution does not prove causation.
- **Bounded certification:** A guarantee limited to the explicitly enumerated search space and assumptions. It is not an unrestricted global-optimality guarantee.

Avoid language that implies guilt, criminality, intent, or confirmed fraud from model outputs or graph relationships.

## 7. Pull request expectations

Before submitting a pull request:

1. Check that the proposed change is within the repository's scope.
2. Verify every new factual claim and citation.
3. Check equations, notation, units, and metric denominators.
4. Identify any change to the research methodology.
5. Preserve historical results rather than silently replacing them.
6. Explain what changed and why.
7. Include supporting evidence for experimental claims.

Pull requests that change reported results should explain whether the change is a correction, a new experiment, a reproduction, or a methodological revision.

## 8. Data and artifact restrictions

Do not upload:

- Private or real financial transaction records.
- Personally identifiable information.
- Authentication tokens, API keys, passwords, or private certificates.
- Unlicensed third-party datasets or software.
- Large model checkpoints without explicit approval.
- Unverified files presented as original or recovered research artifacts.

Synthetic datasets and generated examples must be clearly identified as synthetic.

## 9. License and ownership

Unless a separate license file explicitly grants permission, the presence of material in this repository should not be interpreted as granting permission to reuse, redistribute, modify, or commercially exploit it.

Third-party materials remain subject to their respective licenses and terms.

## 10. Final principle

The objective is not to maximize reported performance. It is to maintain a technically defensible, transparent, and reproducible research record.

A contribution that identifies a limitation or invalidates an earlier claim is valuable when it improves the accuracy of the research.
