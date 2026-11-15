# Provenance and Evidence

## 1. Purpose

This document defines how research claims, experimental results, model artifacts, and historical records are identified and interpreted in the PAC-CF repository.

The project experienced checkpoint loss, SSD failure, implementation revisions, and invalidated experimental versions.

For this reason, provenance is essential.

The repository must not imply that an artifact was recovered, reproduced, or independently verified unless the available evidence supports that statement.

## 2. Evidence categories

Use the following categories when documenting claims.

| Category | Meaning |
|---|---|
| Original historical record | A report or result recorded during the original research execution. |
| Recovered artifact | An artifact retrieved from an available backup or archive. |
| Reconstructed artifact | An artifact recreated or retrained after the original was lost. |
| Independently reproduced | A result reproduced through a separately documented execution. |
| Implementation test | A test of a software component or invariant. |
| Synthetic validation | A deterministic test using constructed synthetic examples. |
| Derived calculation | A value calculated from other recorded values. |
| Unverified claim | A claim that lacks sufficient supporting evidence. |
| Invalidated result | A result that must not be used as definitive evidence because of a known defect or protocol problem. |

A claim may have more than one relevant category, but the category must be stated clearly.

## 3. Original TGAT checkpoint history

The original final A4 checkpoint was recorded at:

```text
research/checkpoints/official_a4_optimized/tgat_checkpoint_best.pt
```

The original final A5 checkpoint was also stored in the original research checkpoint area.

Both were deleted during a cleanup operation associated with a legacy test.

The available backup archive did not contain the final original A4 or A5 checkpoint.

It contained an earlier TGAT checkpoint with:

- Best epoch: 1.
- Best validation ROC-AUC: approximately 0.9256816.
- A different checkpoint identity from the final A4 record.

This earlier checkpoint must not be represented as the final A4 checkpoint.

## 4. Backup archive

The historical backup archive was recorded at:

```text
C:\Users\Deepak\Projects\AGT_Financial_Crime_Intelligence.7z
```

Recorded size:

```text
7,157,623,882 bytes
```

The archive integrity check was reported as successful.

The archive was extracted read-only to:

```text
C:\Users\Deepak\Recovery\AGT_FCI_A4_A5\
```

The archive contained an earlier checkpoint, but not the final original A4/A5 checkpoint files.

The archive's integrity does not establish that it contains every historical project artifact.

## 5. Reconstructed A4-equivalent checkpoint

The reconstructed checkpoint is recorded at:

```text
research/reconstruction/a4/run_final/tgat_checkpoint_latest.pt
```

SHA-256:

```text
1CE6BB6C2D1C3239C88DE862E9D66A2D0A22894993542F27343D17F57CE18FE5
```

This checkpoint was trained from scratch using the documented reconstruction configuration.

It is a reconstructed A4-equivalent checkpoint.

It is not the original A4 checkpoint and must not be labeled as recovered original evidence.

## 6. Dataset and manifest identity

Recorded dataset SHA-256:

```text
B19D39F515523373F991B689C07E11E7B0B95C17A2C27A87D91584AE16C5B040
```

Recorded target manifest SHA-256:

```text
07253F252C812232EA1E2FE699442C281EE7EA133B0C8164FAB5FEF815A43918
```

These hashes identify the recorded dataset and target manifest versions.

A file with a matching filename but a different hash must not be treated as the same artifact.

## 7. Historical A4 and A5 metrics

The original A4 report recorded:

- Validation ROC-AUC: 0.9425262.
- Best epoch: 8.
- Early-stop epoch: 13.
- PR-AUC at best ROC-AUC: approximately 0.7627253.

The original A5 report recorded:

- Seed: 43.
- Best epoch: 4.
- Validation ROC-AUC: 0.9373508.
- PR-AUC: 0.7468530.
- Early-stop epoch: 6.

These are historical reported metrics.

The missing original checkpoints prevent current independent inference verification against those exact original model artifacts.

## 8. Reconstructed multi-seed evidence

The reconstructed runs recorded the following validation results:

| Seed | ROC-AUC | PR-AUC |
|---:|---:|---:|
| 42 | 0.9422524 | 0.7415535 |
| 43 | 0.9439040 | 0.7709527 |
| 44 | 0.9414502 | 0.7517185 |
| 45 | 0.9430912 | 0.7939649 |

Recorded mean ROC-AUC:

```text
0.942674431447517
```

Recorded ROC-AUC standard deviation:

```text
0.0010586836
```

These values belong to the reconstructed runs.

## 9. Counterfactual result artifacts

The project contains historical PAC-CF V1, V2, and V3 evaluation records.

V1 is a historical development result with documented defects.

V2 is an invalidated evaluation because of stale-score behavior after projection.

V3 is the later projection-aware bounded-search design.

The V3 evidence includes separate 479-target and 500-target records.

The 500-target deduplicated result artifact has recorded SHA-256:

```text
9F875BAFCFA702CE12BF471BE000EFDDAF54F1E3D172EC608AE41F8062B73133
```

The raw 500-target file had 517 chronological lines due to duplicate entries associated with overlapping processes.

The deduplicated artifact must be distinguished from the original raw file.

The V3 status counts recorded in different summaries do not fully reconcile. The primary machine-readable result artifacts must be inspected before publication.

## 10. Invalid mock manifest

A mock target manifest was identified as invalid.

Recorded hash:

```text
C620363...
```

The full hash should be taken from the original audit artifact before being added to a formal evidence manifest.

This mock manifest must not be used as the final evaluation population.

## 11. WFFC evidence

The WFFC implementation was tested using deterministic synthetic cases and an independent exhaustive pure-Python oracle.

The recorded test suite included 14 passing tests.

A later PAC-CF invariant suite contained 20 synthetic invariants.

These tests support the behavior of the implementation in the tested cases.

They do not prove that WFFC models real bank balances or all financial transactions.

## 12. Application evidence

The B1–B7 application development records include:

- Inference parity.
- Graph state lifecycle.
- API behavior.
- Integrated Gradients implementation.
- Graph explanation snapshots.
- Frontend workflow.
- End-to-end validation.
- Release hardening.

The recorded release-hardening report identified three defects and reported all three resolved.

The underlying report and test outputs should be preserved alongside any claim that depends on them.

## 13. Evidence hierarchy

When evidence conflicts, use the following order of preference:

1. Original machine-readable experiment artifacts with verified hashes.
2. Original execution logs and configuration files.
3. Independent validation outputs.
4. Checkpoint and dataset metadata.
5. Signed or otherwise traceable audit reports.
6. Historical summaries and narrative reports.
7. Unverified recollections or derived summaries.

This hierarchy is a practical documentation rule. It does not mean that every machine-readable artifact is automatically correct.

Artifact integrity and scientific validity are separate questions.

## 14. Claim traceability

Every major quantitative claim should be associated with:

- Claim identifier.
- Experiment identifier.
- Dataset hash.
- Checkpoint hash.
- Target manifest hash.
- Configuration.
- Result artifact.
- Validation procedure.
- Evidence category.
- Known limitations.

A future `claims-register.md` should contain a claim-by-claim mapping.

## 15. Publication rule

Do not publish a numerical result as a definitive finding if:

- Its denominator is unclear.
- Its source artifact cannot be identified.
- Its checkpoint identity is unknown.
- Its evaluation population is ambiguous.
- Its result was invalidated by a known defect.
- Conflicting result summaries remain unresolved.

Where evidence is incomplete, preserve the claim as historical or unverified and state the limitation explicitly.
