# Structured GS for SLAM

## Definition

在 SLAM 中使用 anchor / scaffold 组织的结构化 3D Gaussian 表示，替代 free Gaussian cloud。Anchor 位置 + feature + MLP decoder → neural Gaussians。SEGS-SLAM (Scaffold-SLAM) / Scaffold-GS / OG-Mapping / VPGS 等共用此思想。

## Why it matters

Free Gaussian 在 SLAM 中易冗余增长、几何漂浮、过拟合、跨输入模式迁移差。Structured GS 让 Gaussian 生长受 anchor / scaffold / frequency / LOD 控制，更适合长期 mapping。

## Related notes

- [[gs-slam/structured/SEGS-SLAM]]
- [[Appearance-Geometry-Decoupling]]
- [[Frequency-Pyramid-Growth]]
