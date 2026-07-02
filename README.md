# Online Streaming Learning of ECG-Based Cancellable Biometric Recognition using Different Random Projection Methods

This repository contains the experimental code and final result outputs for a master's thesis on ECG-based cancellable biometric recognition in an online streaming learning setting.

The project studies how different Random Projection (RP) methods transform ECG feature representations into cancellable templates, and how these transformed templates perform under the same RanPAC online learning protocol. The evaluation considers recognition utility, biometric error behaviour, online stability, efficiency, and cancellable template protection indicators.

Raw ECG data and large intermediate `.npy` / `.npz` files are not included in this repository because of file size and biometric data considerations. The repository provides the experimental notebooks, execution records, final evaluation tables, figures, and reports.

## Research Area

This work sits at the intersection of:

- ECG biometric recognition
- cancellable biometrics
- random projection methods
- online / streaming learning
- biometric template protection
- performance and security-oriented evaluation

The central idea is to transform ECG feature representations into replaceable RP-based templates, then evaluate whether these transformed templates can still support stable subject recognition as ECG segments arrive sequentially.

## Dataset and Signal Setup

The experiments use the UofT ECG Database (UofTDB), focusing on the sit condition.
For request dataset UofTDB please follow this link: https://www.comm.utoronto.ca/~biometrics/databases.html

- Sampling rate: 200 Hz
- Segment length: 5 seconds
- Samples per segment: 1000
- Number of ECG segments: 36,103
- Number of subjects: 1,019
- Stream order: time-aware order based on week number, record number, and segment index
- Task: closed-set multi-class ECG biometric identification

## Method Overview

The experimental pipeline follows these stages:

1. Parse and clean UofTDB ECG signals.
2. Segment ECG recordings into fixed 5-second non-overlapping windows.
3. Build a time-aware ECG stream.
4. Extract three ECG feature representations:
   - PQRST-oriented fiducial features
   - UofTDB self-trained ResNet1D embeddings
   - concatenated PQRST + ResNet1D features
5. Apply Random Projection transformations to generate cancellable templates.
6. Evaluate projected templates with RanPAC online learning under a prequential test-then-train protocol.
7. Compute recognition metrics, including accuracy, macro F1, and EER.
8. Evaluate online stability, runtime, and multi-seed robustness.
9. Evaluate cancellable template indicators, including irreversibility, unlinkability, and revocability.

## Experimental Design

The final evaluation uses a multi-seed RP design:

- Feature settings: 3
- RP methods: 5
- Projection dimensions: 3
- RP seeds: 3
- Total RP-based RanPAC experiments: 135

Feature settings:

- `fiducial_pqrst`
- `embedding_resnet1d`
- `both_pqrst_resnet1d`

Random Projection methods:

- Gaussian RP
- Sparse RP
- Sign RP
- SRHT
- Bernoulli-Gaussian RP

Projection dimensions:

- 100
- 200
- 500

## Evaluation Metrics

The evaluation reports both recognition performance and cancellable template indicators.

Recognition metrics:

- Enrollment-aware accuracy
- Enrollment-aware macro F1
- Enrollment-aware EER
- Runtime
- Online stability curves

Cancellable template indicators:

- Normalized Reconstruction Error (NRE) for irreversibility
- Reconstruction SNR for reconstruction quality
- `Dsys` for unlinkability across projection keys
- Revocability pseudo-impostor EER across projection keys

Lower recognition EER is better. Higher NRE and lower reconstruction SNR indicate stronger reconstruction resistance. Lower `Dsys` indicates stronger unlinkability. Revocability pseudo-impostor EER closer to 0.5 indicates stronger revocability.

## Main Results

The strongest recognition setting in the final multi-seed evaluation is:

```text
Feature setting: embedding_resnet1d
RP method: bernoulli_gaussian
Projection dimension: 500
Setting ID: emb_bg_d500
```

Mean results across three RP seeds:

| Metric | Value |
| --- | ---: |
| Enrollment-aware accuracy | 0.818863 |
| Enrollment-aware macro F1 | 0.785632 |
| Enrollment-aware EER | 0.214210 |

Group-level recognition findings:

- ResNet1D embedding features achieved the strongest overall recognition performance.
- Bernoulli-Gaussian RP achieved the best method-level mean recognition result.
- Gaussian RP and Sparse RP remained very close to Bernoulli-Gaussian RP in the controlled embedding + 500 dimension setting.
- Projection dimension 500 gave the strongest recognition performance within the tested range, with higher runtime and template size.
- Projection dimension 200 provided a lighter middle option with lower runtime.

## Cancellable Template Evaluation

The cancellable template evaluation changes the interpretation from a single recognition ranking into a utility-protection trade-off.

Mean indicators by RP method:

| RP method | Accuracy | NRE ↑ | SNR (dB) ↓ | Dsys ↓ | RevEER → 0.5 |
| --- | ---: | ---: | ---: | ---: | ---: |
| Sign | 0.4883 | 2.403 | -2.39 | 0.065 | 0.506 |
| Sparse | 0.5221 | 0.061 | 94.33 | 0.116 | 0.511 |
| Gaussian | 0.5201 | 0.062 | 94.31 | 0.131 | 0.497 |
| Bernoulli Gaussian | 0.5239 | 0.062 | 94.31 | 0.152 | 0.499 |
| SRHT | 0.4366 | 0.077 | 70.60 | 0.154 | 0.488 |

Main interpretation:

- Bernoulli-Gaussian RP gives the strongest recognition result.
- Sign RP gives the strongest reconstruction resistance and the strongest unlinkability result.
- Sparse RP provides a practical middle option because its recognition performance is close to the strongest group while its `Dsys` is lower than Gaussian RP and Bernoulli-Gaussian RP.
- The revocability pseudo-impostor EER values are close to 0.5 for all five RP methods, which indicates similar revocability behaviour under projection seed replacement.

## Repository Structure

```text
data/
  raw/                         Raw and parsed UofTDB files
  processed/                   Cleaned signals, feature matrices, RP outputs, RanPAC outputs

notebooks/
  01_data_check.ipynb
  02_preprocessing.ipynb
  03_segmentation.ipynb
  04_stream_preparation.ipynb
  05_feature_representation_ablation_v5.ipynb
  06_v6_rp.ipynb
  07_v6_ranpac.ipynb
  08_v6_eval.ipynb

results/
  eval_v6/
    tables/                    Final result tables and rankings
    figures/                   Evaluation figures
    eer_curves/                EER curve outputs
    reports/                   Final evaluation report
```

## Key Output Files

Recognition and ranking outputs:

- Final per-experiment table: `results/eval_v6/tables/final_v6_135.csv`
- Multi-seed mean/std table: `results/eval_v6/tables/mean_std_v6.csv`
- Balanced ranking: `results/eval_v6/tables/top10_balanced.csv`
- Feature comparison: `results/eval_v6/tables/by_feature.csv`
- RP method comparison: `results/eval_v6/tables/by_method.csv`
- Projection dimension comparison: `results/eval_v6/tables/by_dim.csv`
- Final report: `results/eval_v6/reports/v6_report.md`

Cancellable template evaluation outputs:

- Irreversibility runs: `results/eval_v6/tables/security_irreversibility_runs.csv`
- Unlinkability and revocability seed pairs: `results/eval_v6/tables/security_unlinkability_revocability_seed_pairs.csv`
- Security evaluation by setting: `results/eval_v6/tables/security_evaluation_by_setting.csv`
- Security evaluation by RP method: `results/eval_v6/tables/security_evaluation_by_method.csv`
- Security evaluation notes: `results/eval_v6/reports/security_evaluation_notes.md`
- Security evaluation figure: `results/eval_v6/figures/security_evaluation_by_method.png`

