# Primitive Birth Provenance

## Definition

GS/anchor primitive 记录其来源信息：birth keyframe ID、birth packet ID、supporting observations、last confirmed frame、source depth/normal estimator、maturity state。DGS-SLAM 的 Gaussian source keyframe ID 是简化版本。

## Why it matters

Primitive 不应只是一个 3D 几何点。它的出生来源信息本身就是图结构信号。可用于：loop-aware reobservation、anchor maturity update、static support confirmation、dynamic-risk decay/increase、submap overlap verification、source estimator 可靠性回溯。

## Related notes

- [[DGS-SLAM]]
- [[Dynamic-Gate-Through-Pipeline]]
- [[Robust-Mask-Residual]]
