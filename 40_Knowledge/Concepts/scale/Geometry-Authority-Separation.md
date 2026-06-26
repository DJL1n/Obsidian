# Geometry Authority Separation

## Definition

在 GS-SLAM 中，明确区分几何权威与渲染表达：pose/depth/scale 由 DROID/DSPO/DPVO-style joint optimization 决定（非 GS render loss），3DGS map 只消费和表达这些几何结果，并随几何更新变形。Splat-SLAM 是代表实现。

## Why it matters

GS render residual 很容易把几何错误吃掉（opacity/scale/color 调整可以补偿 pose/depth 误差）。Splat-SLAM 证明：dense flow / DSPO / DBA 作为几何 authority，GS 只作为 downstream map，rendering 质量仍然很好。支持你的 "no GS feedback into pose/depth/anchors" 边界。

## Related notes

- [[gs-slam/monocular/Splat-SLAM]]
- [[Deformable-GS-Map]]
- [[Proxy-Depth-Packet]]
