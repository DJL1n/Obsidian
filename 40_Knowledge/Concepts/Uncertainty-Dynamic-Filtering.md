# Uncertainty Dynamic Filtering

## Definition

在 monocular GS-SLAM 中，通过 per-sequence uncertainty（而非语义类别/RGB-D mask）降权动态/遮挡/不一致区域，同时作用于 tracking DBA 和 GS mapping。WildGS-SLAM 是代表实现：DINOv2 feature + online uncertainty MLP。

## Why it matters

语义类别 dynamic SLAM 需要预定义 classes，不够灵活。Uncertainty-based 方法可以适配任意场景的未知干扰物。WildGS-SLAM 证明 DINOv2 + online MLP 在 dynamic scenes 中明显优于 YOLO+SAM mask，且同时改善 tracking 和 mapping。

## Related notes

- [[WildGS-SLAM]]
- [[Static-Dynamic-Side-Channel]]
- [[Certified-Dynamic-Gate]]
