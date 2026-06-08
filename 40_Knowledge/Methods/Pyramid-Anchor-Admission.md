# Pyramid Anchor Admission

## Goal

将 RGBDS-SLAM 的 MLP-GS pyramid 思想迁移到 anchor skeleton，实现 coarse-to-fine anchor 准入，避免一次性在 noise level 上接受所有几何细节。

## Design

| Level | Structure | Evidence needed |
|---|---|---|
| 0 | 大面 / 低频 | depth consensus, DPVO temporal |
| 1 | 中频边界 / 法线过渡 | + normal consistency, MASt3R |
| 2 | 高频细节 / 高梯度 | + multiview support, low residual |

## Related notes

- [[RGBDS-SLAM]]
- [[Multi-Level-Pyramid-GS]]
- [[Multi-Channel-Gaussian-Attributes]]
