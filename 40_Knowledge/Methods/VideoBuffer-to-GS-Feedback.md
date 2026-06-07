# VideoBuffer to GS Feedback

## Goal

将 GS-SLAM 的 coarse-to-fine reliable Gaussian selection 思想迁移到你的 VideoBuffer / anchor 系统中，定义 GS feedback 的安全门：什么条件下 GS rendered signal 可以/不可以参与 frontend 或 geometry decision。

## Background

GS-SLAM 的 coarse-to-fine tracking 证明：GS map 中的坏/漂浮 Gaussians 会污染 pose。因此 GS feedback 必须只使用经过认证的可靠区域。

## Procedure

### GS feedback is valid ONLY if ALL:
1. Pixel's rendering Gaussian comes from a certified anchor
2. Cumulative alpha / silhouette > threshold (SplaTAM-style)
3. Pixel corresponds to a region with low residual history
4. Region has stable multi-frame visibility
5. Gaussian is not recently born (< N frames ago)
6. No active geometry dispute (DPVO/MASt3R disagreement)

### If GS feedback is used:
- Only as weak evaluator (weight < 0.1 in combined loss)
- NOT as direct VideoBuffer truth
- NOT as CertifiedGeometryPacket content
- NOT as frontend pose correction without DPVO verification

### If violation detected (e.g., high render residual + low anchor confidence):
- Quarantine child Gaussian
- Demote parent anchor confidence
- Trigger re-evaluation of anchor maturity

## Related notes

- [[GS-SLAM]]
- [[Coarse-to-Fine-GS-Tracking]]
- [[Adaptive-Gaussian-Expansion]]
- [[Silhouette-Gated-Tracking]]
