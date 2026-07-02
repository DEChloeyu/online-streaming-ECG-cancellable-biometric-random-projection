# Supplementary Cancellable Template Security Evaluation

This section is a supplementary evaluation for cancellable biometric properties. It uses existing random-projection outputs and does not rerun RanPAC.

## Metrics

1. Irreversibility is evaluated by a known-projection reconstruction stress test. The transformed template is used with the stored projection matrix or SRHT parameters to reconstruct the standardized input feature. Higher normalized reconstruction error and lower reconstruction SNR indicate stronger resistance to feature recovery.

2. Unlinkability is evaluated by cross-key template comparison. For two different random seeds, same-sample cross-key cosine scores are compared with different-subject cross-key cosine scores. The reported Dsys score is defined as 2 * abs(AUC - 0.5). A value closer to 0 means stronger unlinkability in this evaluation.

3. Revocability is evaluated by pseudo-impostor matching across different projection keys. The reported pseudo-impostor EER is computed from the same cross-key score distributions. A value closer to 0.5 indicates stronger revocability because old and new templates are harder to match.

## Evaluation Scope

Sample size used: 36103 ECG segments.

The results are supplementary security-oriented indicators. They should be interpreted together with recognition accuracy, macro F1, EER, runtime, and online stability.
