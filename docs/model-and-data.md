# Model and Data

## 1. Overview

The PAC-CF research uses a temporal graph learning setup for synthetic anti-money-laundering transaction data.

The principal model is a Temporal Graph Attention Network (TGAT).

The dataset context is IBM AMLSim HI-Small.

The model is treated as frozen during counterfactual explanation. PAC-CF searches over graph interventions without retraining the predictor.

## 2. Dataset

### 2.1 IBM AMLSim HI-Small

The documented dataset is the HI-Small configuration from IBM AMLSim.

AMLSim is a synthetic anti-money-laundering simulator.

It generates artificial financial transaction records for research into transaction patterns and simulated suspicious activity.

The dataset must not be described as a collection of real bank transactions or confirmed financial-crime cases.

Official project:

https://github.com/IBM/AMLSim

### 2.2 Dataset scale

The research records describe a dataset scale of approximately:

- 518,581 accounts.
- 5,078,345 transactions.

These figures refer to the documented research dataset configuration. The exact processed dataset and preprocessing pipeline should be verified using the corresponding artifact hashes before reproduction.

### 2.3 Dataset identity

The recorded dataset hash is:

```text
B19D39F515523373F991B689C07E11E7B0B95C17A2C27A87D91584AE16C5B040
```

The hash identifies the recorded dataset artifact. A reproduction must verify that the file being used produces the same hash before treating it as the same dataset version.

## 3. Temporal graph representation

Accounts are represented as graph nodes.

Transactions are represented as directed temporal edges.

A transaction edge contains source and destination account identifiers, transaction amount, transaction time, and categorical or numerical attributes.

The graph context for a target transaction is restricted to the permitted historical period.

The documented temporal field is `rel_minute`.

The implementation must use consistent temporal ordering for graph construction, inference, and counterfactual evaluation.

## 4. TGAT architecture

The research model uses a TGAT architecture with the following documented configuration:

| Parameter | Value |
|---|---:|
| Input node feature dimension | 16 |
| Edge feature dimension | 39 |
| Hidden dimension | 64 |
| Time dimension | 64 |
| Attention heads | 4 |

The model uses temporal graph attention to aggregate information from time-aware graph neighborhoods.

Reference:

Xu et al. (2020), *Inductive Representation Learning on Temporal Graphs*.

https://arxiv.org/abs/2002.07962

## 5. Node features

The documented node-feature construction uses a 16-dimensional all-ones vector:

\[
\mathbf{x}_v=\mathbf{1}_{16}
\]

This is a specific experimental representation. It should not be interpreted as a rich account profile or as evidence that the model has access to real customer attributes.

## 6. Edge features

The documented edge feature vector has 39 dimensions:

| Feature group | Dimensions |
|---|---:|
| Amount received | 1 |
| Amount paid | 1 |
| Receiving currency one-hot encoding | 15 |
| Payment currency one-hot encoding | 15 |
| Payment format one-hot encoding | 7 |
| **Total** | **39** |

The amount transformation is:

\[
x_{\mathrm{amount}}=\frac{\log(1+x)}{10}
\]

The transformation is applied to the relevant amount fields according to the recorded preprocessing implementation.

Categorical features are represented using the documented one-hot encodings.

Unseen categories during inference are represented using zero-vectors under the documented inference behavior.

## 7. Temporal representation

The temporal field is:

```text
rel_minute
```

Temporal neighborhood construction uses the target time to restrict which edges are eligible for context.

The documented temporal inclusion rule is:

\[
t_{\mathrm{edge}}\leq t_{\mathrm{target}}
\]

The implementation must preserve deterministic ordering for equal timestamps.

No future transaction should influence a target's historical explanation.

## 8. Predictor checkpoint history

The original final A4 and A5 checkpoints were lost during a repository cleanup incident.

They must not be described as recovered.

A backup archive contained an earlier checkpoint, not the final A4 or A5 model.

The recovered earlier checkpoint had:

- Best epoch: 1.
- Best validation ROC-AUC: approximately 0.9256816.

It is not equivalent to the final A4 checkpoint.

### 8.1 Reconstructed A4-equivalent checkpoint

A later reconstruction was trained from scratch using the documented configuration.

Checkpoint path in the historical research workspace:

```text
research/reconstruction/a4/run_final/tgat_checkpoint_latest.pt
```

Recorded SHA-256:

```text
1CE6BB6C2D1C3239C88DE862E9D66A2D0A22894993542F27343D17F57CE18FE5
```

The reconstruction used:

- Seed 42.
- HI-Small cleaned transaction data.
- The recorded target-sample split.
- Validation split.
- Adam optimizer.
- Learning rate: 0.001.
- Batch size: 1024.
- Maximum epochs: 50.
- Early-stopping patience: 5.
- RTX 3060 Laptop GPU.

This checkpoint is an A4-equivalent reconstruction for research continuity. It is not the original A4 checkpoint.

## 9. Multi-seed reconstruction results

The documented reconstructed multi-seed results are:

| Seed | Best epoch | Validation ROC-AUC | Validation PR-AUC | Stop epoch |
|---:|---:|---:|---:|---:|
| 42 | 4 | 0.9422524 | 0.7415535 | 9 |
| 43 | 4 | 0.9439040 | 0.7709527 | 9 |
| 44 | 3 | 0.9414502 | 0.7517185 | 8 |
| 45 | 9 | 0.9430912 | 0.7939649 | 14 |

Recorded summary:

| Metric | Mean | Standard deviation |
|---|---:|---:|
| ROC-AUC | 0.9426744 | 0.0010587 |
| PR-AUC | 0.7645474 | 0.0230920 |

These results describe the reconstructed runs, not the lost original A4/A5 checkpoints.

## 10. Original A4 historical record

The original A4 historical report recorded:

| Metric | Value |
|---|---:|
| Best validation ROC-AUC | 0.9425262 |
| Best epoch | 8 |
| Early-stop epoch | 13 |
| PR-AUC at best ROC-AUC | approximately 0.7627253 |

The original A4 architecture configuration was:

- Input dimension: 16.
- Edge dimension: 39.
- Hidden dimension: 64.
- Time dimension: 64.
- Attention heads: 4.

The recorded A4 result is historical evidence. The original final checkpoint is not available for current independent inference verification.

## 11. Original A5 historical record

The original A5 historical report recorded:

| Metric | Value |
|---|---:|
| Seed | 43 |
| Best epoch | 4 |
| Validation ROC-AUC | 0.9373508 |
| PR-AUC | 0.7468530 |
| Early-stop epoch | 6 |

The original A5 checkpoint is also unavailable.

## 12. Inference behavior

The documented inference implementation uses:

- Evaluation mode.
- Read-only model parameters.
- No-gradient inference.
- CPU fallback where required.
- Deterministic feature preprocessing.
- Temporal graph context construction.

The B1 inference implementation achieved exact parity with a frozen validation target logit in the documented test.

Recorded target logit:

```text
-4.9926300048828125
```

This is a specific parity test, not a claim that every possible inference path has been independently verified.

## 13. Dataset and model limitations

- AMLSim is synthetic.
- Node features are intentionally limited.
- Dataset balances are not complete real account balances.
- Model scores are not automatically calibrated probabilities.
- The original final A4/A5 checkpoints are unavailable.
- Reconstructed results must remain separately identified.
- A model trained on this dataset is not automatically suitable for real-world AML deployment.

## 14. Reproduction requirements

A reproduction should preserve:

1. Dataset identity and preprocessing.
2. Target split and manifest.
3. Model architecture.
4. Checkpoint identity.
5. Feature dimensions and transformations.
6. Temporal ordering.
7. Inference implementation.
8. Evaluation threshold.
9. Software and hardware environment.

Any deviation must be recorded rather than silently treated as equivalent.
