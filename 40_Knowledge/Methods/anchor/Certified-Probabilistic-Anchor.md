# Certified Probabilistic Anchor

## Goal

将 UP-SLAM 的 probabilistic anchor update（Bayesian, dynamic probability）扩展到你的 CertifiedAnchor，形成多证据概率认证。

## Design

UP-SLAM ProbabilisticAnchor: motion/occupancy probability only
Your CertifiedAnchor: richer evidence fusion

### Evidence sources
- Temporal support (DPVO survival + residual)
- Pose-depth-scale consistency
- Normal consistency
- Static probability (multi-view visibility)
- Dynamic risk (DINO uncertainty + MASt3R disagreement)
- Free-space consistency
- GS supporting alpha/silhouette
- Submap ownership

### Bayesian update form
prior anchor probability + current observations (multi-evidence) + likelihood model → posterior anchor probability / maturity score。

### Admission gate
Anchor admitted as CertifiedAnchor only if posterior probability > multi-source agreement threshold。

### Pruning
Low probability anchors: quarantine or demote (not immediate deletion — can recover)。

## Related notes

- [[UP-SLAM]]
- [[Probabilistic-Anchor-Update]]
- [[Multi-Modal-Uncertainty]]
