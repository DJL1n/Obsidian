# Ray Camera Model

## Definition

将 dense pointmap 归一化为 ray 方向：`ray(p) = normalize(X(p))`。每一帧的 pointmap 定义了一个 implicit central camera model，所有光线通过唯一相机中心。

## Why it matters

传统相机模型需要已知固定内参（针孔、fisheye 等）。Ray camera model 让 SLAM 系统可以不依赖固定内参，每帧都有自己的隐式相机模型，理论上能处理 zoom、畸变、甚至 time-varying camera model。

## Key distinctions

| | Traditional pinhole | Ray camera model |
|---|---|---|
| Intrinsics | 固定，需已知 | 隐式，从 pointmap 导出 |
| 表示 | projection matrix K | ray direction per pixel |
| 变焦/畸变 | 需重新标定 | 逐帧自适应 |
| 优化空间 | pixel space | ray angular space |

## Common failure modes

- MASt3R 训练数据主要是 pinhole images，强畸变下 ray prediction 可能退化
- 框架支持 generic camera ≠ 网络已对所有 camera model 鲁棒

## Related notes

- [[MASt3R-SLAM]]
- [[Ray-Residual]]
