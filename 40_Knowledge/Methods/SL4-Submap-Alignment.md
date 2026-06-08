# SL4 Submap Alignment

## Goal

在 feed-forward reconstruction SLAM 中，用 SL(4) 15-DOF homography（非 Sim(3)）对齐子图，处理未标定单目相机下 projective ambiguity。VGGT-SLAM 的实现：5-pt RANSAC → linear solve → determinant-normalize → LM on Lie algebra。

## Key insight

未标定单目 VGGT 子图之间的几何偏差可能不只是 scale，还可能包含 shear/stretch/perspective。Sim(3) 不够表达。但 SL(4) 太自由：可能引入额外 drift（shear/stretch/perspective）。

## For your system
安全做法：在线用 Sim(3)/scale-aware consistency，低频用 SL(4) diagnostic 检测 non-Sim(3) residual。SL(4) 不应直接作为 pre-certification authority。

## Related notes

- [[VGGT-SLAM]]
- [[Projective-Ambiguity-Awareness]]
- [[Feed-Forward-Submap]]
