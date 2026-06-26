# Multi-Level Pyramid GS

## Definition

在 GS-SLAM mapping 中，通过构建多分辨率 image pyramid（low→mid→high），先低分辨率优化全局结构，再逐步提高分辨率恢复细节。RGBDS-SLAM 的 MLP-GS 是代表实现。

## Why it matters

普通 GS 直接在高分辨率优化，细节容易局部过拟合、全局一致性弱。Pyramid 让优化过程先对齐低频结构，再逐步接受高频细节。对 monocular anchor admission 也可借鉴：先确认低频结构一致，再逐级接受中频边界和高频细节。

## Related notes

- [[mapping-reconstruction/RGBDS-SLAM]]
- [[Multi-Channel-Gaussian-Attributes]]
- [[Pyramid-Anchor-Admission]]
