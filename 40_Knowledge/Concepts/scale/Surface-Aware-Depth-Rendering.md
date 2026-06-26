# Surface-Aware Depth Rendering

## Definition

在 GS-SLAM 中，不直接使用 alpha blending 的 Gaussian center depth 作为 rendered depth，而是通过 ray-surface intersection（unbiased depth）+ median depth visibility weighting + accumulation normalization 得到多视角一致的 rendering depth。GauS-SLAM 是代表实现。

## Why it matters

3D Gaussian depth 有 geometry distortion 和 blending 污染。如果 rendered depth 要用于 tracking，必须 surface-aware、multi-view consistent。Ablation 证明去掉 unbiased depth 后 ATE 从 0.60 mm 恶化到 2.10 mm。

## Related notes

- [[3dgs-slam/GauS-SLAM]]
- [[Local-Map-Tracking]]
- [[Surfel-Anchor-Bridge]]
