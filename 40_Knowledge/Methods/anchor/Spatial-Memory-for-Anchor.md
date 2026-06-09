# Spatial Memory for Anchor Skeleton

## Goal

将 Spann3R 的 spatial memory 设计思想迁移到 anchor skeleton / CertifiedGeometryPacket，构建可查询、可遗忘、可修剪的长期几何记忆系统。

## Background

Spann3R 的 memory 设计（working + long-term + attention + clipping + sparsification）与你想做的 anchor skeleton 有很强的结构对应。但 Spann3R 没有 BA / global correction，你的系统需要结合多种证据源。

## Translation mapping

| Spann3R memory | Anchor skeleton equivalent |
|---|---|
| Working memory (5 frames dense) | Recent active anchors / packet cache |
| Long-term memory (sparse top-k) | Mature certified anchors |
| Memory key/value | Anchor feature + support evidence |
| Query feature | Current frame's candidate region |
| Cross-attention retrieval | Anchor support score computation |
| Attention clipping | Outlier / low-confidence hard reject |
| Memory sparsification (top-k) | Only keep geometry-stable, high-support anchors |
| Working → long-term drain | Anchor maturity promotion |

## Key principles

1. Not all DPVO patches / MASt3R points → long-term memory. Must gate: repeated visibility + low residual + geometry-stable + high support + low dynamic risk.
2. Weak evidence should not accumulate infinitely. Low-confidence / outlier anchors must be hard-clipped or quarantined.
3. Memory must be correctable. Spann3R lacks BA → early error contaminates memory. Add GO-SLAM-style global correction: when BA changes pose/depth, affected memory entries must be updated or invalidated.

## Related notes

- [[Spann3R]]
- [[Dense-Sparse-Memory]]
- [[Spann3R-Memory-Architecture]]
- [[Global-Correction-Mapping]]
