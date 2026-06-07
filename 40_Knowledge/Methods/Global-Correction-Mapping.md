# Global Correction Mapping

## Goal

在 GS-SLAM 系统中，当 global BA / loop closure 修正了 keyframe poses 和 depths 后，系统性地刷新受到影响的 submap/packet/Gaussian 区域，确保 map 始终反映最新全局几何状态。

## Background

GO-SLAM 的 instant mapping 选择 pose/depth 变化最大的 keyframes 更新 SDF map。对应到 GS-SLAM，需要类似的 geometry-version-aware refresh 策略。

## Refresh set selection

```
refresh_set =
  latest keyframes
+ never-consumed certified packets
+ geometry-version-change 最大的 packets (top K)
+ stratified old packets (防遗忘)
+ loop-affected packets
```

## Procedure

1. **Snapshot**: 记录当前所有 keyframe 的 pose/depth version
2. **Diff**: 比较当前版本与上次 mapping 时的版本 → 计算每个 keyframe 的变化量
3. **Select**: 按 refresh set 策略选择需更新的 packets
4. **Recertify**: 对受影响的 candidate packets 重新运行认证 gate
5. **Re-evaluate**: 已入 GS 的 anchors 根据最新 geometry 调整 maturity/confidence
6. **Update submap**: 只更新 version 变更的 submap 区域

## Key principles

- 不全局重训 GS map（太贵）
- 只 refresh 受 global correction 影响的区域
- 旧 geometry 不继续当作真值
- Mapping 必须知道 geometry version 变了

## Related notes

- [[GO-SLAM]]
- [[Versioned-Geometry-Update]]
- [[GO-SLAM-Architecture]]
