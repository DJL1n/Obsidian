# SDAR Mechanism

## Goal

在 monocular GS-SLAM 中建立 sparse VO ↔ dense depth ↔ Gaussian map 之间的尺度一致性闭环。

## Procedure

### Step 1: Sparse → Dense correction
Sparse point cloud → project to prior depth camera → sparse depth map。假设 prior depth + sparse depth 的统计分布服从正态分布 → 用均值和标准差做线性校正。

### Step 2: Corrected depth → Gaussian init
Corrected prior depth → backproject → downsample → new Gaussians。

### Step 3: Gaussian → Frontend feedback
Gaussian-rendered depth → frontend tracking frame depth init → scale closure。

## Key insight
SDAR 是 MGS-SLAM 最有价值的机制。但统计校正（mean/std）不够强；你的版本应升级为 multi-factor certification。

## Related notes

- [[MGS-SLAM]]
- [[Sparse-Dense-Scale-Alignment]]
- [[Predicted-Depth-GS-Supervision]]
