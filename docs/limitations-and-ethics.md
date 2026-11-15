# Limitations and Ethical Considerations

## 1. Purpose

PAC-CF is a research method for explaining the behavior of a temporal graph neural network under specified counterfactual constraints.

Its outputs must be interpreted carefully, particularly in financial-crime research.

A model-level counterfactual is not a real-world finding of fraud, money laundering, intent, or criminal responsibility.

## 2. Synthetic dataset limitation

The principal experimental dataset is IBM AMLSim HI-Small.

AMLSim produces synthetic financial transaction data.

Synthetic data is useful for controlled experimentation, but it cannot fully represent:

- Real banking behavior.
- Real customer diversity.
- Actual institutional controls.
- Real financial products.
- Complex settlement mechanisms.
- Real-world adversarial behavior.
- The full distribution of legitimate and suspicious transactions.

High performance on a synthetic dataset does not establish high performance on real bank data.

## 3. Model limitations

The predictor is a TGAT model trained under a specific dataset and preprocessing configuration.

Its output depends on:

- Training data.
- Feature construction.
- Model architecture.
- Learned parameters.
- Temporal neighborhood construction.
- Threshold selection.

The documented node features are 16-dimensional all-ones vectors.

This is a limited representation and does not encode a complete customer profile.

The model score is not automatically a calibrated fraud probability.

## 4. Checkpoint provenance limitation

The original final A4 and A5 checkpoints were lost.

A later A4-equivalent reconstruction was trained from scratch.

The reconstructed checkpoint is not the original A4 checkpoint.

Consequently:

- Historical original A4 results must remain labeled as historical.
- Reconstructed results must remain labeled as reconstructed.
- The two must not be silently combined.
- A reproduction must identify the exact checkpoint hash used.

## 5. WFFC limitation

WFFC is a weak flow-consistency model under partial balance observability.

It uses a maximum-deficit initialization to estimate the minimum starting balance required by the factual flow sequence under the model's assumptions.

This is not an observed bank balance.

WFFC does not account for every possible source or use of funds, including off-graph deposits, withdrawals, credit facilities, fees, reversals, settlement delays, and other banking operations.

A WFFC-valid intervention is valid under the implemented WFFC model. It is not proof of real-world financial feasibility.

## 6. Temporal limitation

Temporal validity is determined by the implementation's ordering and historical-context rules.

A temporally valid graph intervention does not establish that the graph contains every relevant event.

Missing transactions, incomplete histories, timestamp inaccuracies, and unobserved external activity can affect interpretation.

## 7. Counterfactual interpretation

A counterfactual intervention identifies a change to the graph representation that changes the frozen model's output.

It does not establish that the removed transactions caused the underlying real-world behavior.

A counterfactual is not a causal finding about a person, account holder, or institution.

The method explains model sensitivity under its intervention definition.

## 8. Certification limitation

PAC-CF performs exact search only within a bounded intervention space.

A minimum-cardinality certificate is valid only when every feasible candidate of smaller cardinality within the declared search space has been exhaustively checked.

It does not establish:

- Unrestricted global optimality.
- Optimality over unenumerated graph edges.
- Optimality under alternative feasibility models.
- Optimality over all possible intervention types.
- Optimality for real-world financial activity.

The word “certified” must always be accompanied by the bounded-search qualification.

## 9. Attribution limitations

The application includes Integrated Gradients.

Attribution values depend on the model, input representation, baseline, and integration procedure.

An attribution value does not prove that a transaction caused the model decision.

Attribution should not be interpreted as a legal or factual assessment of a transaction.

## 10. Baseline comparison limitations

CoDy and TemGX have different native search procedures and design assumptions.

Their output counts, runtime, and intervention sizes may not be directly comparable without a common evaluation protocol.

Post-hoc WFFC filtering changes the interpretation of a baseline's native output.

All comparisons must identify:

- Target population.
- Predictor and checkpoint.
- Candidate history.
- Native method configuration.
- External feasibility validation.
- Fidelity validation.
- Runtime measurement boundary.

## 11. Statistical limitations

Reported metrics are subject to the limitations of the target population and experimental protocol.

A result from a finite synthetic evaluation set should not be generalized to all AML environments.

Statistical significance should not be claimed without an appropriate test and a clearly defined paired or unpaired comparison.

## 12. Operational limitations

The research application is not established as a production banking system.

Known implementation limitations include:

- An in-memory transaction store that resets on application restart.
- Duplicate transaction facts potentially represented by separate UUID-backed edges.
- Dependence on the availability and integrity of the configured model checkpoint.
- Dependence on the exact preprocessing and graph-context implementation.

Production use would require additional engineering, security, privacy, reliability, and governance work.

## 13. Human oversight

If methods of this kind are considered for investigative workflows, outputs should be treated as decision-support information only.

A qualified human investigator must independently examine the underlying evidence.

No automated model output or counterfactual should independently trigger:

- A finding of guilt.
- Account closure.
- Asset seizure.
- Customer blacklisting.
- Law-enforcement action.
- A legal or regulatory conclusion.

Any operational use would require institution-specific validation, legal review, governance, and human oversight.

## 14. Privacy and data protection

Researchers must not publish personally identifiable information or confidential transaction data.

Dataset use must follow the original provider's terms and applicable laws.

Logs, screenshots, reports, and examples should be checked for secrets and sensitive information before publication.

## 15. Responsible terminology

Use:

- “Model score.”
- “Model prediction.”
- “Counterfactual intervention.”
- “Historical transaction context.”
- “WFFC-valid under the implemented assumptions.”
- “Model-attributed feature contribution.”

Avoid unsupported claims such as:

- “Confirmed fraud.”
- “Criminal network.”
- “Proven money-laundering path.”
- “The transaction caused the fraud.”
- “The account is guilty.”
- “The model proves suspicious intent.”

## 16. Responsible research statement

PAC-CF is an experimental method for constrained counterfactual explanation of temporal graph model predictions.

Its results should be used to understand model behavior and evaluate research hypotheses.

They should not be treated as independent evidence of criminal conduct or as a substitute for financial investigation.
