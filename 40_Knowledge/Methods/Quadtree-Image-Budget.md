# Quadtree Image Budget

## Goal

在 GS mapping 中，根据图像对比度（contrast）递归分割 RGB 图像，将 Gaussian budget 分配给视觉上重要的区域（边缘/细节），低纹理大面积只放少量 Gaussian。GSFusion 的设计。

## Why it matters

Uniform sampling 或 per-pixel birth 浪费 Gaussian 在低纹理区域。Quadtree 让 birth 位置分配与图像复杂度一致：低纹理大 cell → 少 Gaussian；高对比度小 cell → 多 Gaussian。这是 image-space budget allocation 的简单有效版本。

## Your system adaptation
可替换为 anchor support region / stable patch / surfel primitive。不是必须用 quadtree，但思想可借：Gaussian/anchor birth 应该考虑 image-space evidence，不是 uniform。

## Related notes

- [[GSFusion]]
- [[Geometry-Gated-GS-Birth]]
- [[Hybrid-TSDF-GS-Map]]
