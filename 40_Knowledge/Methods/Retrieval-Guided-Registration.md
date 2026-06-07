# Retrieval-Guided Registration

## Goal

在 incremental dense reconstruction 中，不只用最近帧作为参考，而是从历史 scene frames 中检索与当前 keyframe 最相关（visual similarity + baseline suitability）的子集作为 L2W 输入，实现 implicit relocalization 和降低 drift。

## Background

SLAM3R 的 retrieval module 比"只选最近十帧"的 baseline 显著改善性能。correlation score 不只是 image feature cosine similarity，还考虑 3D registration suitability（类似 I2P final prediction 的 mean confidence）。

## 对 SkelGS-SLAM 的借鉴

不只是 temporal neighborhood；还要从历史中检索与当前几何相关的 reference frames / anchors / packets。可用于：
- Current candidate packet → historical certified anchors / submaps
- Loop candidate verification
- Submap overlap reference selection

## Related notes

- [[SLAM3R]]
- [[I2P-Local-Reconstruction]]
- [[L2W-Global-Registration]]
