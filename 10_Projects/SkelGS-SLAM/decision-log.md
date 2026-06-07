# SkelGS-SLAM — Decision Log

## 2026-06-07 — MASt3R-SLAM 论文分析结论

### 背景
完整阅读并整理了 MASt3R-SLAM (CVPR 2025) 论文，评估其作为 GS 前端的可行性。

### 关键判断

**MASt3R-SLAM 的定位：强 geometric proposal generator，不是 final certified GS geometry provider。**

#### 可采用的机制
1. **Ray residual** — GeometryPacket 的一致性应分为：
   - Strong term: angular / ray / bearing consistency
   - Weak term: depth / distance / scale consistency
   - Late term: GS rendering utility
   - 不要直接 3D point residual 主导 pose 或 anchor admission。

2. **Canonical pointmap fusion** — raw MASt3R packet → candidate packet → fused canonical packet → GS birth packet。GS 不应该直接吃 raw two-view output。

3. **Loop edge 需要 geometry verification** — retrieval candidate 不能直接成为约束。

4. **Scale/gauge 显式建模** — 不能只靠后处理 scalar。

#### 不建议照搬的
- MASt3R-SLAM 没有 full global geometry refinement
- 不输出 GS-ready uncertainty
- 不处理 dynamic ownership

### 对 DROID/DPVO 的定位
Temporal trust / lifecycle / uncertainty 更可能来自 DPVO/DROID-style signals，而非 MASt3R-SLAM。

### 后续方向
- 吸收 ray-consistency 和 canonical fusion 机制
- 保留 DPVO/DROID 的 temporal optimisation 优势
- 验证 anchor skeleton 中的 lifetime/uncertainty 信号来源

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/MASt3R-SLAM]]
