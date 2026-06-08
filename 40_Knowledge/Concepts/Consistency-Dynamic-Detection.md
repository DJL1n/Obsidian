# Consistency Dynamic Detection

## Definition

通过比较历史 Gaussian map 渲染（color + depth）与当前 RGB-D 观测来判断动态区域，而不是依赖语义类别。ADD-SLAM 是代表实现：历史 GS map render vs current observation → color/depth inconsistency → MobileSAM mask。

## Why it matters

语义类别方法需要预定义动态类（人/车/动物），但会把"语义上可动但当前静止"的物体错误过滤。Consistency-based 方法更接近 SLAM 本质：运动破坏历史-当前一致性，不依赖预设类别。ADD-SLAM 消融显示一致性检测优于 MaskDINO 语义分割。

## Key insight
这是 pre-GS consistency oracle，不是 post-GS map render residual。你的系统应改为 DPVO/DROID window depth-pose + stable anchor + certified candidate。

## Related notes

- [[ADD-SLAM]]
- [[Occlusion-vs-Exposure-Cue]]
- [[Pre-GS-Consistency-Oracle]]
