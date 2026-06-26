# Frontend Gaussian Initialization

## Definition

在 GS-SLAM 中，利用 VO/frontend 已有的计算产物（image gradients、keyframe poses、pixel associations、semi-dense depth）不只是初始化 Gaussian center，还初始化 covariance / orientation / scale，避免 heuristic 初始化。

## Why it matters

普通 KNN/constant/heuristic 初始化需要额外 1200–2700 iterations 才能收敛到同等质量。利用 frontend 已有信息直接从结构初始化 Gaussian shape，显著减少优化成本，提升重建质量（GSO-SLAM ablation: KNN 25.51 PSNR → proposed 34.48）。

## Procedure

Image gradients → 2D covariance Σ2D。Multiple co-visible keyframes → consolidated Σ3D。Eigen-decomposition → rotation R + scale S。Min scale → 0 for 2DGS surface splat。

## Related notes

- [[slam-frontends/gpu-optimized/GSO-SLAM]]
- [[EM-VO-GS-Coupling]]
- [[Certified-EM-Coupling]]
