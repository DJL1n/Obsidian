# Observed vs Hypothesized Geometry

## Goal

在 GS-SLAM 中区分两类 geometry：真实多视角观测认证的几何（Observed CertifiedGeometry）和基于结构先验/假设推断/插值预测的几何（Hypothesized InferredGeometry），让系统能同时利用二者但不混淆。

## Why it matters

MG-SLAM 的 surface interpolation 基于 Manhattan 假设补全未观测区域。这些 hypothesized Gaussians 能提升 rendering completeness，但不能等同于真实观测的几何锚点。你的 CertifiedGeometryPacket 体系必须分层。

## Layers

| Layer | Source | Can constrain pose | Can gate GS birth |
|---|---|---|---|
| Observed CertifiedGeometry | multi-view DPVO/MASt3R/depth-normal | Yes | Yes |
| Hypothesized InferredGeometry | Manhattan/semantic/line/plane prior | No | Rendering only |

## Related notes

- [[MG-SLAM]]
- [[Manhattan-Structure-Prior]]
- [[Structure-Guided-GS-Completion]]
