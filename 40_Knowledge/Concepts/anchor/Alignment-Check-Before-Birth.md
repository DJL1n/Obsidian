# Alignment Check Before Birth

## Definition

在新增 Gaussian / anchor 之前，先用 ICP / registration 检查新点是否与已有 mature support 对齐，alignment fitness 差则不允许写入 mature anchor，只保留为 weak candidate。FlashSLAM 在插入新 Gaussian 前对可见 Gaussians 做 ICP correction。

## Why it matters

单帧 depth/gradient 不足以决定 anchor birth。新增 primitive 必须和已有几何结构对齐，否则会被局部噪声 / 动态 / 预测误差误导。Alignment check 让 birth 更安全。

## Related notes

- [[slam-frontends/gpu-optimized/FlashSLAM]]
- [[Explicit-Tracking-vs-Render-Tracking]]
- [[Loss-Weighted-Refinement]]
