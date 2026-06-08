# VGGT as Geometry Candidate

## Goal

将 VGGT 用作 SkelGS-SLAM 的离线几何候选，不作为在线 SLAM 前端，而是作为 teacher / verifier / GS birth candidate source。

## Design

### Offline teacher
- VGGT keyframe window inference → camera + depth + pointmap + confidence
- 与 DPVO/DROID/HI-SLAM2 的 pose/depth 做一致性比较
- 只作为 shadow evidence，不写回 VideoBuffer / GS

### CertifiedPacket candidate
VGGTGeometryPacket:
  T_cw, K, depth, pointmap, confidence, track_features, scale_stat, reprojection_stat
Status: read-only candidate. Not VideoBuffer truth. Not GS consumption source.

### GS birth candidate
VGGT depth + camera → unproject pointcloud → candidate for anchor/Gaussian initialization
（VGGT 实验证明 depth+camera unproject > point head）

### Anchor evidence
Tracking features → cross-view consistency → anchor static_score, repeatability, depth_stability

## Not for
- Online SLAM frontend
- VideoBuffer writeback
- Direct anchor birth without multi-frame verification

## Related notes

- [[VGGT]]
- [[Multi-View-Geometry-Packet]]
- [[Feed-Forward-vs-Optimization]]
