# Robust Mask Residual

## Goal

在 dynamic GS-SLAM 中，用 photometric residual histogram 生成 outlier mask，补偿 segmentation 边界、阴影、artifact 和漏分割。

## Procedure

1. Render image from Gaussian map
2. Compute per-pixel photometric residual vs input image
3. Sort residuals → histogram
4. Percentile threshold → classify inliers/outliers
5. Smooth mask (normalized kernel) to avoid high-frequency texture 误杀
6. Iteratively update histogram during window optimization

## Risk
闭环风险：map error → residual 高 → 被当成 outlier → 不修正 → error 固化。

## Related notes

- [[DGS-SLAM]]
- [[Dynamic-Gate-Through-Pipeline]]
- [[Primitive-Birth-Provenance]]
