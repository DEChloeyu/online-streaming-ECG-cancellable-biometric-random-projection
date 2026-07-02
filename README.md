# Online Streaming Learning of ECG-Based Cancellable Biometric Recognition using Different Random Projection Methods

This project studies ECG-based biometric recognition in an online streaming setting. The main research focus is how different Random Projection (RP) methods can be used as cancellable biometric transformations, and how their transformed ECG templates perform under the same RanPAC online learning protocol.

Due to file size and biometric data considerations, raw ECG data and intermediate .npy/.npz files are not included in this repository. The repository provides the full experimental notebooks, execution records, final evaluation tables, figures, and reports.

## Research Area

This work sits at the intersection of:

- ECG biometric recognition
- cancellable biometrics
- random projection methods
- online / streaming learning
- template protection and performance evaluation

The central idea is to transform ECG feature representations into cancellable projected templates, then evaluate whether the transformed templates can still support stable subject recognition as ECG segments arrive sequentially.

## Dataset and Signal Setup

The experiments use the UofT ECG Database (UofTDB), focusing on the sit condition.

- Sampling rate: 200 Hz
- Segment length: 5 seconds
- Samples per segment: 1000
- Number of ECG segments: 36,103
- Number of subjects: 1,019
- Stream order: time-aware order based on week number, record number, and segment index

## Method Overview

The experimental pipeline follows these stages:

1. Parse and clean UofTDB ECG signals.
2. Segment ECG recordings into fixed 5-second windows.
3. Build a time-aware ECG stream.
4. Extract three ECG feature representations:
   - PQRST-oriented fiducial features
   - UofTDB self-trained ResNet1D embeddings
   - concatenated PQRST + ResNet1D features
5. Apply Random Projection transformations.
6. Evaluate projected templates with RanPAC online learning.
7. Compute final Accuracy, Macro-F1, EER, ranking tables, and figures.

## Experimental Design

The final V6 evaluation uses a multi-seed RP design:

- Feature settings: 3
- RP methods: 5
- Projection dimensions: 3
- RP seeds: 3
- Total RanPAC experiments: 135

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

## Main Results

The best overall setting in the final multi-seed evaluation is:

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
| Enrollment-aware macro-F1 | 0.785632 |
| Enrollment-aware EER | 0.214210 |

Group-level findings:

- ResNet1D embedding features achieved the strongest overall recognition performance.
- Bernoulli-Gaussian RP achieved the best method-level mean enrollment-aware accuracy.
- Projection dimension 500 gave the best recognition performance, with higher runtime and template storage cost.

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
    reports/                   Final V6 evaluation report
```

## Key Output Files

- Final per-experiment table: `results/eval_v6/tables/final_v6_135.csv`
- Multi-seed mean/std table: `results/eval_v6/tables/mean_std_v6.csv`
- Balanced ranking: `results/eval_v6/tables/top10_balanced.csv`
- Feature comparison: `results/eval_v6/tables/by_feature.csv`
- RP method comparison: `results/eval_v6/tables/by_method.csv`
- Projection dimension comparison: `results/eval_v6/tables/by_dim.csv`
- Final report: `results/eval_v6/reports/v6_report.md`



Large binary experiment artifacts are tracked with Git LFS. After cloning, install Git LFS and run:

```bash
git lfs pull
```
