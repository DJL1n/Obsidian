# Explicit Tracking vs Render Tracking

## Definition

GS-SLAM 中两种 tracking 范式的对比：Explicit tracking（SuperPoint/LightGlue + RGB-D 3D-3D SVD registration）vs Render tracking（constant velocity init + Gaussian render-loss gradient descent）。

## Why it matters

Render tracking 在 dense high-FPS 场景可用，但在 sparse / large-motion / noisy depth 条件下收敛差。Explicit matching + 3D registration 在这些条件下更稳。FlashSLAM 的 sparse setting 实验（stride=40 时 SplaTAM 542 cm vs FlashSLAM 6 cm）直接支撑这一判断。

## Key insight for your project
GS render loss 适合 refine，不适合作为 primary pose solver。支持你当前不让 GS 反写前端的边界。

## Related notes

- [[slam-frontend/FlashSLAM]]
- [[Alignment-Check-Before-Birth]]
- [[Loss-Weighted-Refinement]]
