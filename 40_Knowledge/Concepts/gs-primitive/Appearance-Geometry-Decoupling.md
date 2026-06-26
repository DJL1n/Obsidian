# Appearance-Geometry Decoupling

## Definition

在 GS-SLAM 中将 appearance variation 与 geometry 分离建模：appearance embedding (AfME) 吸收 view-dependent color / exposure / lighting 变化，防止 render residual 错误地驱动 geometry update。SEGS-SLAM 的 AfME 是代表实现。

## Why it matters

Appearance mismatch 常导致 GS 不必要地移动/变形 Gaussians 去解释颜色差异。显式分离后：render residual 高 → 先尝试 appearance embedding 解释；只有 appearance 解释不了 + geometry evidence 支持 → 才允许 geometry change。这对你"geometry first, GS after certification"是直接支持。

## Related notes

- [[semantic/SEGS-SLAM]]
- [[Structured-GS-for-SLAM]]
- [[Frequency-Pyramid-Growth]]
