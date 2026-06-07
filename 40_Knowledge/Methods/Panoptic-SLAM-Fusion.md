# Panoptic SLAM Fusion

## Goal

将 2D panoptic segmentation 的输出转化为 3D SLAM map 中稳定的语义状态，使 map points 具有 semantic class 和 instance ID。

## Procedure

### Step 1: Keyframe segmentation
在 selected keyframes 上运行 panoptic segmentation network（如 Detectron2）。

### Step 2: Semantic probability fusion
对同一个 3D map point 的多次观测，用 Bayes-rule 风格融合其 semantic probability distribution：

map point P_j 被多个 keyframes 观测到，每次观测落在一个 pixel 上，pixel 有 semantic class probability。多次观测融合成 P_j 的 class probability distribution，得到 temporal consistent semantic map。

### Step 3: Instance tracking
Panoptic segmentation 的 instance ID 在不同帧间不一致。两阶段策略：
- **2D IoU tracking**: 当前帧 instance 与前后帧同类 instance 计算 IoU，取最大值
- **3D cluster merge**: 如果 centroid distance < threshold 且 >80% descriptors match，则 merge clusters

### Step 4: Cluster creation
按 semantic class + instance ID 将 3D map points 分组：
```
cluster C_k = {points belonging to same semantic class and instance}
```

## Key design choices

- 不需要每帧 segmentation（低频分割对 mapping accuracy 影响小）
- Fusion 使得即使单帧 segmentation 有噪声，map-level semantic label 更稳定
- Cluster merge 防止 object 离开再回来时无限创建新 cluster

## 对 SkelGS-SLAM 的借鉴

低频语义 side-channel + 高频 tracking 主链的设计：语义信息更适合做 delayed constraint，不直接塞进 tracking 主链。

## Related notes

- [[S3LAM]]
- [[Semantic-Cluster-Structure-Prior]]
- [[Structured-Anchor-Group]]
