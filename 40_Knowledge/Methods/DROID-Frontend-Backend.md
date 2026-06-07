# DROID Frontend + Backend System Architecture

## Goal

DROID-SLAM 的双线程异步 SLAM 系统结构：frontend 处理实时视频流做 local BA，backend 做全局 BA over keyframe history。

## Procedure

### Frontend
1. 新帧进入：extract features, add frame to graph
2. 与 mean optical flow 最近的 3 个邻居连边
3. 用 linear motion model 初始化 pose
4. 运行若干次 update operator（recurrent correction + DBA）
5. 更新 keyframe poses 和 depths
6. Keyframe removal:
   - 如果某些 frame pair 平均 flow 很小 → 冗余 keyframe 删除
   - 否则删除最旧 keyframe

### Backend
1. 对所有 keyframe pairs 计算 flow-based distance matrix
2. 先加 temporal edges
3. 按 flow distance 从小到大采样 covisibility edges
4. 抑制邻近重复 edges
5. 对整个 frame graph 运行 update operator
6. 做 global dense BA

### Non-keyframe tracking
- Keyframes: full dense BA (pose + inverse depth)
- Non-keyframes: motion-only BA，估计相对邻近 keyframes 的 pose

## Key design choices

- Frontend 和 backend 运行在独立 Python processes（asynchronous mode）
- Backend 用 RAFT memory-efficient correlation volume 避免爆显存
- Keyframe management 基于 dense flow distance，非 sparse feature count

## 对 SkelGS-SLAM 的借鉴

DROID 的双线程结构可以作为 anchor skeleton 的参考：
- **Frontend-thread**: 维护 active window anchors，local temporal consistency
- **Backend-thread**: 全局 anchor graph optimisation，loop closure 后 anchor 重生/纠正

## Related notes

- [[DROID-SLAM]]
- [[Dense-Bundle-Adjustment]]
- [[Recurrent-Correction-Update]]
