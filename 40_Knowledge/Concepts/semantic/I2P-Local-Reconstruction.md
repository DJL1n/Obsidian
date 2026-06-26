# I2P Local Reconstruction

## Definition

在 RGB-only dense reconstruction 中，通过 sliding window 将短视频片段内的多帧图像聚合为 keyframe-referenced dense 3D pointmap。SLAM3R 的 I2P 是代表实现：multi-branch ViT + multi-view cross-attention + max-pooling aggregate multi-frame evidence。

## Why it matters

Pairwise pointmap（DUSt3R）只看到两帧，multi-view window 可从多个 supporting frames 汇聚几何证据，减少单对图像的遮挡/基线/baseline 问题。I2P 提供了 local window 内的密集、鲁棒、keyframe-centered geometry packet。

## Related notes

- [[geometry-model/SLAM3R]]
- [[L2W-Global-Registration]]
- [[Retrieval-Guided-Registration]]
