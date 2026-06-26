# 3D-Grounded Matching

## Definition

将 image matching 重新表述为 3D 问题：两个像素是否匹配取决于它们是否观察到同一个 3D point。通过预测 shared-coordinate pointmaps（两帧像素映射到同一 3D 坐标系），从 3D 空间而非 2D descriptor space 中寻找 correspondences。

## Why it matters

传统 2D descriptor matching 在大视角变化、低纹理、重复纹理下容易失败。3D-grounded matching 利用对称的、learned 3D prior 来驱动匹配，相比纯 2D 方法对 extreme viewpoint 变化更鲁棒。MASt3R 的 Map-free 结果证明了这一点。

## Key distinctions

| | 2D descriptor matching | 3D-grounded matching |
|---|---|---|
| 匹配空间 | descriptor space | 3D coordinate space + feature space |
| 视角鲁棒性 | 弱 | 强 |
| Keypoint 依赖 | 需要 | detector-free |
| 额外输出 | 无 | pointmap + depth |

## Related notes

- [[geometry-model/MASt3R]]
- [[Two-View-Pointmap-Prior]]
- [[Fast-Reciprocal-Matching]]
