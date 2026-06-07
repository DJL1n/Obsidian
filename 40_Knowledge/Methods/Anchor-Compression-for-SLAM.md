# Anchor Compression for SLAM

## Goal

将 ContextGS 的 anchor-level context compression 思想迁移到 SkelGS-SLAM 的 frozen / mature submap 存储，实现在保持渲染质量的同时大幅降低长期地图存储体积。

## Background

ContextGS 证明 anchor-based GS map 的 anchor features 有强烈空间邻域相关性，可通过 autoregressive context model 压缩 ~15×。这对长期 SLAM 地图存储至关重要。

## Design

### 三层地图生命周期
| Layer | Update rate | Precision | Compression |
|---|---|---|---|
| Active submap | 每帧 | full | none / light |
| Mature submap | 低频 | structured | Scaffold-style |
| **Frozen / archived** | **从不** | **context-coded** | **ContextGS-style** |

### Frozen submap compression pipeline
1. CertifiedAnchorOctree → partition into hierarchical levels
2. Train/apply anchor-level context model (offline or periodic batch)
3. Entropy code L0 → context predict L1 → context predict L2
4. Store compressed bitstream

### Key principle
ContextGS anchor = rendering/compression unit. Your CertifiedAnchor = geometry certification unit. Anchors must be certified BEFORE being compressed for frozen storage.

## Related notes

- [[ContextGS]]
- [[Anchor-Level-Context-Model]]
- [[Hierarchical-Anchor-Partition]]
- [[Rate-Distortion-GS]]
