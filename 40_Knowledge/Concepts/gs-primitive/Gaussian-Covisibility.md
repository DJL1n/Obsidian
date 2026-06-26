# Gaussian Covisibility

## Definition

在 GS-SLAM 中，两个 keyframe 之间的 covisibility 通过它们共同观测到的 3D Gaussians 的 IoU（intersection over union）来衡量。一个 Gaussian 是否在某个视角可见的判断依据：被 rasterisation 使用且当前 ray 累积 α 未达到 0.5。

## Why it matters

传统 covisibility 依赖 sparse feature matches 或 image overlap 启发式。Gaussian covisibility 利用 rasterisation 的 alpha compositing 天然处理 occlusion，无需额外估计。这使得 keyframe selection 和 submap overlap 判断可以直接基于 GS map 本身的结构。

## Related notes

- [[3dgs-slam/MonoGS]]
- [[GS-Tracking-Representation]]
- [[Isotropic-Regularization]]
