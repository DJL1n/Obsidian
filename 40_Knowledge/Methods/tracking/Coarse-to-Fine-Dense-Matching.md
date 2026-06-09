# Coarse-to-Fine Dense Matching

## Goal

在有限分辨率主干（最大边 ~512 px）基础上，对高分辨率图像实现像素精确的 dense correspondences，不因下采样损失精度。

## Background

MASt3R 主干输入分辨率有限。高分辨率图像如果直接缩小再上采样，correspondences 会损失 pixel accuracy。对 SfM/localization，几像素误差明显影响 pose。

## Procedure

1. 低分辨率 / zoomed-out 图像上做粗匹配
2. 根据粗匹配确定局部区域
3. 在高分辨率 crop 中细化匹配
4. 得到精确 pixel correspondences

## Key design points

- 粗匹配提供全局初始对应
- 局部 crop 恢复高分辨率细节
- 对 SfM/localization 关键

## Related notes

- [[MASt3R]]
- [[Fast-Reciprocal-Matching]]
- [[3D-Grounded-Matching]]
