# Predict-and-Refine Gaussian

## Definition

在 monocular GS-SLAM 中，Gaussian 的 position / color / opacity / scale / rotation 不由每帧从零 start-from-scratch optimization 决定，而由 recurrent feed-forward model 直接预测，backend 只做轻量 refine。Flash-Mono 是代表实现。

## Why it matters

传统每关键帧 250+ iterations rendering optimization 是 monocular GS-SLAM 速度瓶颈。Feed-forward predict 将 Gaussian birth 变成一次前向，大幅降低 mapping 成本。但 predict 出的 Gaussian 仍需 admission test 才能进入 persistent map。对应你的系统：FeedForwardGaussianCandidate → admission → CertifiedGeometryPacket / GS birth。

## Related notes

- [[3dgs-slam/Flash-Mono]]
- [[Hidden-State-Submap-Descriptor]]
- [[FeedForward-Gaussian-Candidate]]
