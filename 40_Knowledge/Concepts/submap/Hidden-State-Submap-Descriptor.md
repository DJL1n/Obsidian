# Hidden-State Submap Descriptor

## Definition

在 recurrent feed-forward SLAM 中，将 recurrent hidden state 缓存为 submap 级别的压缩描述子，用于 loop closure / relocalization / scale comparison。Flash-Mono 的 Bag of Hidden States 是代表实现。

## Why it matters

传统 loop descriptor（NetVLAD / DINO 等）是纯视觉。Hidden state 携带多帧视觉+几何上下文，一次前向可直接产生 relocalized pose + geometry。但 hidden state 不是权威位姿，仍有遗忘和漂移问题，需 submap management 和 PGO 配合。

## Related notes

- [[3dgs-slam/Flash-Mono]]
- [[Predict-and-Refine-Gaussian]]
- [[FeedForward-Gaussian-Candidate]]
