# Fast Reciprocal Matching

## Goal

在 dense feature maps（或 pointmaps）之间高效建立 reciprocal pixel correspondences，避免 O(H₁W₁ × H₂W₂) 的全搜索复杂度。

## Background

MASt3R 的朴素 dense matching 代价极高 — 比生成 dense feature maps 本身更耗时。Fast reciprocal matching 从子集开始，迭代传播，逐步找到 reciprocal cycles，移除已收敛的 correspondences。

## Procedure

1. 从子集开始做 nearest-neighbor search
2. 找到 reciprocal pair (i↔j 且 j↔i)
3. 标记为已收敛，移除
4. 迭代传播到周围区域
5. 持续直到所有 correspondences 收敛

## Backend selection
- 3D point matching: K-d tree
- 24-d local feature matching: FAISS（K-d tree 因 curse of dimensionality 低效）

## Performance
最高可达 64× faster matching（Map-free 上报告）。

## Related notes

- [[MASt3R]]
- [[3D-Grounded-Matching]]
- [[Coarse-to-Fine-Dense-Matching]]
