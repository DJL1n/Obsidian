# Matchability Assignment

## Goal

替代 Sinkhorn optimal transport 用于 sparse local feature matching 的 assignment head。将匹配问题拆解为两个可学习分量：相似度（similarity）和可匹配性（matchability）。

## Background

SuperGlue 用 Sinkhorn 求解 partial assignment。LightGlue 指出 Sinkhorn 的行列归一化计算和显存昂贵，且 dustbin 把所有点的 similarity score 纠缠在一起，训练动态不理想。

## Procedure

### Step 1: Similarity
计算两图 keypoint descriptors 之间的 pairwise similarity matrix。

### Step 2: Matchability
对每个 keypoint 显式预测一个 matchability score（0-1），表示该点是否本就可匹配。不可匹配来源：occlusion、viewpoint change、non-repeatable detection、动态物体、边界、模糊。

### Step 3: Assignment score
P_ij = matchability_i × matchability_j × row-softmax(similarity) × col-softmax(similarity)

### Step 4: Final matching
满足以下条件的 (i, j) 为匹配：
1. P_ij > threshold
2. i→j 是 row maximum
3. j→i 是 column maximum

## Key advantages

- 比 Sinkhorn 轻量，每层都可计算 assignment
- 支持 deep supervision（每层都监督）
- 支持 early stopping（任一层输出都可用）
- Matchability 作为 feature-level reject signal

## Related notes

- [[LightGlue]]
- [[Sparse-Learned-Matcher]]
- [[Adaptive-Computation-Graph]]
