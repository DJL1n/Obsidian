# Dynamic Suppression Both Sides

## Definition

在 dynamic GS-SLAM 中，动态证据不仅用于 tracking mask，也同时影响 Gaussian birth / optimization / pruning。Dy3DGS-SLAM 中 tracking motion loss 和 GS rendering loss 都使用 fused mask。

## Why it matters

只在前端 mask 动态区域，map 仍可能被污染。双侧抑制使动态区域被剔除的同时，map 不会错误写入动态 object。对应你的系统：dynamic probability 应同时影响 tracking weight、anchor birth gate、GS admission 和 rendering loss weight。

## Related notes

- [[Dy3DGS-SLAM]]
- [[Multi-Evidence-Dynamic-Fusion]]
- [[DynamicEvidencePacket]]
