# VarSplat Architecture

## Goal

RGB-D uncertainty-aware submap 3DGS-SLAM: per-splat appearance variance + total variance law rendering + 3-level uncertainty usage。

## Components

- Representation: each Gaussian = μ, α, Σ, SH **+ σ²** (appearance variance)
- Uncertainty rendering: total variance law through alpha blending → per-pixel variance
- Training: NLL-style (square L2 + log σ²), RGB+depth residuals
- Tracking: freeze σ², median-centered log scaling → per-pixel confidence weight → weighted RGB-D pose optimization
- Loop: per-splat variance → submap reliability → rescore NetVLAD
- Registration: uncertainty-weighted photometric loss
- Submap: centroid/tracking uncertainty threshold, TSDF → global GS refinement

## Related notes

- [[VarSplat]]
- [[Primitive-Level-Reliability]]
- [[Appearance-vs-Geometry-Uncertainty]]
