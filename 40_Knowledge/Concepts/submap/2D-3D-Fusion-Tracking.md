# 2D-3D Fusion Tracking

## Definition

在 GS-SLAM 中结合 2D photometric rendering loss 和 3D voxel Gaussian ICP 进行 coarse-to-fine 相机位姿估计，并根据 2D 信息质量动态调整 2D/3D 依赖权重。

## Why it matters

单靠 render loss（SplaTAM/MonoGS style）在 motion blur、exposure change、outdoor illumination 下不稳定。3D ICP 在几何结构丰富场景更鲁棒。2D-3D fusion 让 tracking 跨室内/室外、跨 motion condition 都更可靠。

## Procedure

1. **Coarse**: 2D photometric loss → coarse pose（室内/纹理丰富有效）
2. **Fine**: 3D voxel Gaussian ICP → refine pose（motion blur/exposure/outdoor）
3. **Adaptive**: 2D quality < threshold → skip coarse 2D → direct 3D ICP

## Related notes

- [[VPGS-SLAM]]
- [[Submap-Geometry-Unit]]
- [[Large-Scale-GS-SLAM-Architecture]]
