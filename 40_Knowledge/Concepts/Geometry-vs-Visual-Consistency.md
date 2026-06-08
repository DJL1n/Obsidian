# Geometry vs Visual Consistency

## Definition

在 GS-SLAM 中，geometric alignment (pose/depth 对齐) 不保证 visual consistency (color/opacity/SH/semantics 无缝融合)。CoGS-SLAM 多 agent 场景尤其明显：即使 pose graph 对齐了，不同 agent 的曝光、视角、GS 优化状态不同，仍可能出现 photometric seam。

## Why it matters

这对单机器人系统同样成立：pose/depth 通过几何认证 ≠ GS rendering 不会有 seam。ChildGS/residual appearance layer 应单独处理颜色/曝光/局部残差，不反向污染几何 authority。

## Related notes

- [[CoGS-SLAM-Survey]]
- [[Submap-Level-Provenance]]
- [[Intra-Agent-Submap-Loop]]
