# Uncalibrated Dense Reconstruction

## Definition

在不依赖已知相机内参、外参、位姿或标定的情况下，直接从未知图像对/集合进行密集 3D 重建。DUSt3R 通过 pointmap regression 实现：image pair → shared-coordinate pointmaps → global alignment (multi-view) → dense 3D。

## Why it matters

传统 SfM/MVS 依赖已知/估计的相机参数。对 casual captures、internet photos、unknown cameras、低重叠 image collections 非常不友好。Uncalibrated 范式让 dense 3D 更容易获取。

## Key distinctions

| | Traditional SfM/MVS | Uncalibrated (DUSt3R) |
|---|---|---|
| 需要 K | 是 | 否 |
| 需要 pose | 是 | 否 |
| 需要 matches | 是 | 隐式 |
| 输出 | sparse/dense + pose | dense pointmap |

## Related notes

- [[DUSt3R]]
- [[Pointmap-Regression]]
- [[DUSt3R-Global-Alignment]]
