# Geometry-First GS Pipeline

## Definition

在 GS-SLAM / mapping 中，先建立/认证稳定几何场（SDF / CertifiedGeometryPacket / CertifiedAnchorField），再让 Gaussian 作为 geometry 上的 appearance residual layer 生长，而非让 Gaussian 同时承担几何和外观。

## Why it matters

GPS-SLAM 证明 geometry-first 路线可达 150+ FPS 且高保真。GS 对错误 geometry 敏感 → 先稳定几何再放 GS。也完全支持你"CertifiedGeometryPacket before GS"的方向。

## Contrast

| | Full GS | Geometry-first GS |
|---|---|---|
| 几何 | GS 自由建 | SDF / certified packet |
| GS birth | render gradient | geometry residual on certified surface |
| 安全 | 低 | 高 |
| 速度 | 慢 | 快 |

## Related notes

- [[mapping-reconstruction/GPS-SLAM]]
- [[Residual-GS-Layer]]
- [[Certified-GS-Residual]]
