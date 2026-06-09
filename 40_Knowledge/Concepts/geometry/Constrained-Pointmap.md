# Constrained Pointmap

## Definition

不直接相信神经 pointmap（MASt3R/DUSt3R raw output）作为最终几何，而是将其投影到受约束的几何变量（camera intrinsics + extrinsics + depth）中，使几何服从成像模型。MASt3R-SfM 的关键设计。

## Why it matters

Raw pointmap 是 over-parameterized 表示，表达能力强但可能不满足投影一致性。Constrained pointmap 强制几何符合 pinhole model，减少自由度并提高全局一致性。对应到你的系统：foundation model prior → CertifiedGeometryPacket（受 temporal / scale / normal / free-space 约束）。

## Related notes

- [[MASt3R-SfM]]
- [[Anchor-Depth-Parameterization]]
- [[Offline-SfM-to-Online-Anchor]]
