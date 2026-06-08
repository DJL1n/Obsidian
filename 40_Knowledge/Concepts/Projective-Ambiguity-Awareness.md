# Projective Ambiguity Awareness

## Definition

在 feed-forward reconstruction（VGGT/MASt3R/DUSt3R）中，未标定单目子图之间可能存在 projective ambiguity，不只是 Sim(3) scale gauge。VGGT-SLAM 用 SL(4) 15-DOF homography 处理这种一般化歧义。

## Why it matters

Foundation model 输出的 depth/pose/pointmap 可能不是简单"尺度差一点"，而可能包含 anisotropic stretch、shear、perspective distortion、depth-dependent bias。如果你的 CertifiedGeometryPacket 只做 Sim(3) 或 scalar-scale 检查，可能漏掉更一般的几何污染。提醒：SL(4) 本身太自由，不宜直接作为在线 authority，更适合作为 diagnostic / projective-risk assessment。

## Related notes

- [[VGGT-SLAM]]
- [[SL4-Submap-Alignment]]
- [[Projective-Risk-Diagnostic]]
