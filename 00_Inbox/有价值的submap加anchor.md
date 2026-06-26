是，**submap 融合问题不会因为你换成 anchor-centric 自动消失**。但 anchor 结构确实给你一个更好的切入点：**把融合从 Gaussian 层前移到 anchor 层**，在高斯球膨胀之前解决重复、尺度、重叠和 ownership 问题。

我先说结论：

现有 GS-SLAM 的 submap fusion 多数是在 **submap pose / keyframe pose / Gaussian map** 层面做对齐和合并；你的 anchor-centric 结构最有潜力的点是做 **anchor-level fusion before Gaussian expansion**。

这和普通 keyframe-submap 最大的区别是：  
**别人是先生成很多 Gaussians，再想办法融合；你可以先融合 anchors，再决定哪些 Gaussian cell 需要保留、重采样、蒸馏或删除。**

---

## **1. 现有研究怎么做 submap fusion / loop correction？**

目前大概有四类。

### **1.1 整体 submap 刚性对齐：LoopSplat 路线**

LoopSplat 是比较典型的 **3DGS submap + loop closure** 方法。它把地图分成 3DGS submaps，然后在检测到 loop 后，直接对 3D Gaussian splats 做 registration，得到 submap 之间的相对约束，再做 robust pose graph optimization，最后刚性对齐 submaps 来保证全局一致性。论文明确说它 computes relative loop edge constraints between submaps directly via 3DGS registration，并 rigidly aligns submaps。 

它的逻辑是：

```text
submap A Gaussian map
submap B Gaussian map
        ↓
3DGS registration
        ↓
relative loop constraint
        ↓
pose graph optimization
        ↓
rigidly align submaps
```

优点：

```text
1. 直接利用 3DGS 表示
2. 不需要重新转成传统点云再配准
3. loop closure 后能修正全局漂移
```

缺点：

```text
1. 融合发生得比较晚，Gaussians 已经生成了
2. 重叠区域的重复 Gaussians 仍然需要处理
3. 刚性 submap 对齐对局部尺度/形变误差处理有限
4. 更适合 RGB-D / 有较好几何输入的场景
```

对你来说，LoopSplat 的启发是：**可以直接在 map representation 上做 loop edge**。但你不一定要在 Gaussian 层做，可以在 anchor 层做。

---

### **1.2 keyframe / submap pose 更新后移动 Gaussians：GLC-SLAM / keyframe-owned Gaussians 路线**

GLC-SLAM 的核心是把场景划分成 3D Gaussian submaps，并通过 hierarchical loop closure 和 global-to-local 策略减少漂移；它使用 3D Gaussian submaps 来支持 loop correction 后的大场景地图更新。 

这类方法一般逻辑是：

```text
keyframe / submap pose graph
        ↓
loop closure / PGO
        ↓
更新 keyframe 或 submap 位姿
        ↓
根据 ownership 移动相关 Gaussians
```

优点：

```text
1. 和传统 SLAM pose graph 很兼容
2. 实现相对直接
3. keyframe ownership 清楚
```

缺点也正是你想解决的：

```text
1. Gaussian ownership 绑定 keyframe
2. 重复观测容易重复 birth Gaussian
3. submap overlap 处容易出现冗余和撕裂
4. loop 后移动的是已有 Gaussian，而不是先处理地图结构
```

这类方法的问题可以概括成：

**keyframe 是观测单位，但它被迫承担了地图 ownership。**

你的 anchor-centric 方案正好可以攻击这个点。

---

### **1.3 voxel-anchor + online distillation：VPGS-SLAM 路线**

VPGS-SLAM 是目前最需要警惕的相近工作。它明确说现有 GS-SLAM 在大规模长序列中有 memory explosion，并提出 voxel-based progressive 3D Gaussian representation、multiple submaps、2D-3D Gaussian loop closure，以及 loop 检测时的 submap fusion with online distillation。 

它的局部表示是：

```text
multi-resolution voxel
        ↓
每个 voxel 对应 anchor point
        ↓
anchor spawns neural Gaussians
        ↓
attributes 由 anchor feature 和 viewing position 预测
```

论文也明确说，当 keyframe 到来时，会在该帧观测区域初始化 voxels，每个 voxel 分配一个 anchor point，每个 anchor 生成一组 neural Gaussians。 

它的融合逻辑大概是：

```text
local submaps
+ voxel anchors
+ loop detection / correction
        ↓
online distillation
        ↓
merge loop-related submaps
```

优点：

```text
1. 真的考虑了大规模内存问题
2. anchor / voxel 表示比普通自由 Gaussian 更紧凑
3. 有 submap fusion 和 online distillation
```

但它和你仍有关键区别：

```text
VPGS:
    voxel / keyframe / submap 驱动
    keyframe 到来后扩展 observed region
    anchor 更像 compact representation unit

你想做的:
    temporally certified anchor 驱动
    anchor 是 submap lifecycle 和 map ownership 的中心
    keyframe 只是 observation，不是地图所有者
```

所以 VPGS 不是完全挡死你，但它是最危险的 related work。

---

### **1.4 local submap alignment + global Gaussian memory：SING3R-SLAM 路线**

SING3R-SLAM 是单目方向很相关的近期工作。它认为 3D priors 在长序列里容易产生 drift、scale inconsistency 和 local submap misalignment，于是提出一个 globally consistent Gaussian map，作为 persistent differentiable memory，把 local geometric reconstruction 通过 submap-level global alignment 融入全局 Gaussian map。 

它的核心不是普通拼接，而是：

```text
local 3D reconstruction prior
        ↓
submap-level global alignment
        ↓
global Gaussian map
        ↓
rendering / differentiable BA refinement
```

论文还说，它通过 submap-level local geometry + global Gaussian map 保持 fine structures 和 global consistency，并且性能分析里报告了较小 map size。 

优点：

```text
1. 单目相关性强
2. 强调 global Gaussian memory
3. 不是只做 tracking，而是 joint refine camera poses and scene geometry
```

缺点：

```text
1. 仍然主要是 submap-to-global Gaussian map 的融合
2. anchor ownership 不是核心
3. 对你来说，它更像强 baseline / 思想参照，而不是完全同题
```

---

## **2. 现有 submap fusion 的共同问题**

这些方法虽然细节不同，但大多有一个共同模式：

```text
先生成局部地图
再做 submap 对齐
再融合 / refine / distill
```

也就是：

```text
local Gaussians 已经长出来了
        ↓
发现 loop / overlap
        ↓
再解决重复和不一致
```

问题在于，到了这个阶段，很多错误已经实体化成 Gaussians：

```text
1. 同一表面被多个 submap 重复表示
2. 不同 submap 的 Gaussian 尺度/颜色/opacity 不一致
3. submap 边界处出现双层结构或 floaters
4. loop correction 后需要大规模移动 / prune / distill
5. memory 已经膨胀，融合只是补救
```

所以你的 anchor-centric 结构真正有用的地方不是“融合更容易”，而是：

**让融合发生在更低维、更稳定、更可认证的 anchor 层，而不是膨胀后的 Gaussian 层。**

---

## **3. anchor 结构能给 submap fusion 什么特殊好 idea？**

我认为最值得做的是下面五个。

---

# **Idea 1：Anchor-level fusion before Gaussian-level fusion**

这是最核心的。

现有方法通常是：

```text
Gaussian submap A
Gaussian submap B
        ↓
对齐 / 融合 / 蒸馏
```

你可以改成：

```text
Anchor graph A
Anchor graph B
        ↓
anchor association / duplicate detection / scale alignment
        ↓
merge anchors
        ↓
再决定 Gaussian cells 怎么处理
```

也就是：

```text
先融合地图骨架，再融合渲染细节
```

具体流程：

```text
1. 检测两个 submap 的 overlap anchors
2. 根据 anchor position / normal / descriptor / visibility / scale witness 建立 correspondence
3. 估计 submap-to-submap Sim(3) 或局部 deformation
4. 合并重复 anchors
5. 对 merged anchors 下属 Gaussian cells 做 prune / resample / distill
```

这样比直接 Gaussian fusion 好，因为 anchor 数量远少于 Gaussian 数量，且每个 anchor 有时序支持和几何置信度。

你的论文可以把它叫：

```text
Anchor-First Submap Fusion
```

或者：

```text
Pre-Gaussian Anchor Fusion
```

这有主线价值。

---

# **Idea 2：Anchor overlap 决定 submap 是否需要融合，而不是 keyframe overlap**

传统 submap fusion 常用：

```text
keyframe co-visibility
image retrieval
pose proximity
submap rendering similarity
```

你的 anchor-centric 可以用：

```text
anchor overlap
```

也就是判断：

```text
两个 submap 是否观测到了同一批 mature anchors？
两个 submap 的 anchors 是否在空间、法向、尺度、观测方向上兼容？
两个 submap 的 anchors 是否能解释同一组 keyframe observations？
```

这比 keyframe overlap 更接近地图结构。

可以定义一个 anchor overlap score：

```text
S(A, B) =
    matched_anchor_count
    × normal_consistency
    × scale_consistency
    × visibility_consistency
    × appearance_consistency
    × uncertainty_weight
```

这样 submap fusion 触发条件不再是：

```text
camera 又回到了附近
```

而是：

```text
当前 submap 与历史 submap 共享稳定地图结构
```

这对 monocular 尤其重要，因为单目 pose/scale 会漂，光靠 keyframe proximity 不稳。

---

# **Idea 3：Anchor-level duplicate suppression，避免 Gaussian explosion**

你担心 submap 多了 Gaussian 数量爆炸，这个问题可以用 anchor 层提前抑制。

普通 keyframe-centric 方法是：

```text
新 keyframe 来了
        ↓
看到新区域 / 残差高
        ↓
birth 新 Gaussians
```

但 residual 高不一定代表新结构，可能只是：

```text
pose drift
scale mismatch
颜色未收敛
已有 submap 的重复区域
```

anchor-centric 可以规定：

```text
只有当当前观测无法被已有 nearby anchors 解释时，才允许新 anchor birth；
只有 mature anchor 才能 spawn Gaussian cell；
重叠 submap 中 matched anchor 不再重复 spawn Gaussians。
```

也就是把 Gaussian 数量控制前移：

```text
不是 prune duplicate Gaussians
而是 prevent duplicate anchors
```

这个很重要。

可以设计一个 admission gate：

```text
new_anchor_allowed =
    low_explainability_by_existing_anchors
    AND sufficient temporal support
    AND low dynamic probability
    AND scale-consistent depth
    AND non-duplicate in anchor graph
```

这比后期 prune 高斯更干净。

---

# **Idea 4：Anchor 作为 submap deformation graph**

现有很多 submap 方法是刚性对齐：

```text
submap A undergoes one SE(3) / Sim(3)
```

但实际单目 GS-SLAM 的错误往往不是全局刚性误差，而是：

```text
局部尺度不一致
墙面轻微弯曲
不同窗口深度 gauge 不一致
submap 边界轻微错层
```

anchor-centric 的好处是可以自然变成一个 deformation graph：

```text
每个 anchor 是一个局部控制节点
submap fusion 后，不一定只优化一个 submap pose
而是优化 overlap anchors 的局部 transforms
```

比如：

```text
anchor_i:
    position x_i
    local scale s_i
    local rotation R_i
    confidence / covariance
```

fusion 时优化：

```text
min over anchor transforms:
    anchor correspondence residual
  + rendering residual
  + smoothness residual between neighboring anchors
  + scale consistency residual
```

这样你可以处理：

```text
rigid submap alignment 解决不了的小形变
```

但注意：这个方向实现风险比前两个高。第一版可以只做 rigid / Sim(3) anchor graph fusion，后续再做 deformation。

---

# **Idea 5：Frozen submap 用 ContextGS-like compression，active submap 用 explicit/Scaffold-like cell**

你担心显存，这是合理的。但不要把 ContextGS 放进 active mapping 主循环。更合理的设计是分层：

```text
active submap:
    anchor + explicit small Gaussian cell
    方便优化，方便追踪，方便融合

inactive / frozen submap:
    anchor-level compact feature
    Scaffold-like decoder 或 ContextGS-like compression
    只在需要重激活 / loop / rendering 时解码
```

ContextGS 的核心是 anchor-level context model，报告了相对 vanilla 3DGS 超过 100×、相对 Scaffold-GS 约 15× 的 size reduction，同时保持相当或更高渲染质量。 

但它是 compression / storage design，不是在线 SLAM fusion 方法。所以你可以借鉴为：

```text
frozen anchor cells 的存储压缩
```

而不是：

```text
active SLAM 的主表示
```

这可以形成一个很清楚的内存策略：

```text
active anchors:
    full parameters, optimizable

recent inactive anchors:
    compact Gaussian cell, renderable

old frozen anchors:
    compressed anchor context, load-on-demand

loop overlap anchors:
    temporarily decompress / refine / re-distill
```

这比单纯“把所有 Gaussian 都放显存里”合理很多。

---

## **4. 你的 anchor-centric fusion 可以怎么具体定义？**

我建议你把 submap fusion 分成三层，而不是直接做 global fusion。

### **Layer 1：Anchor graph fusion**

每个 submap 有一个 anchor graph：

```text
G_s = (A_s, E_s)
```

其中：

```text
A_s:
    mature anchors

E_s:
    spatial adjacency
    co-visibility
    depth/normal consistency
    temporal support
```

fusion 先做：

```text
A_i in submap p  ↔  A_j in submap q
```

匹配依据：

```text
position after Sim(3)
normal
scale
local descriptor
observed keyframes
rendered appearance
uncertainty
```

得到：

```text
anchor correspondence set C_pq
```

然后估计：

```text
T_pq = Sim(3) or SE(3)
```

或局部 anchor transforms。

---

### **Layer 2：Anchor ownership merge**

匹配上的 anchors 不要保留两份，而是合并成一个 persistent anchor：

```text
a_new = merge(a_i, a_j)
```

合并时不是简单平均，而是 uncertainty-weighted：

```text
x_new = weighted_mean(x_i, x_j)
normal_new = weighted_normal_average
scale_new = robust_scale_average
confidence_new = accumulated support
```

并保留 provenance：

```text
observed_by_submaps = {p, q}
observed_by_keyframes = union(...)
```

这样 submap 融合后，地图结构不会重复增长。

---

### **Layer 3：Gaussian cell fusion / distillation**

anchor 合并后再处理 Gaussian cell：

```text
cell_i + cell_j → cell_new
```

这里可以有几种策略：

```text
1. keep best:
   保留 render loss 更低、uncertainty 更低的一组

2. merge-prune:
   合并后根据空间距离、opacity、visibility prune

3. re-sample:
   以 merged anchor 的 aligned dense depth / rendered depth 重新采样少量 Gaussians

4. distill:
   用两个旧 cells 渲染出来的 image/depth supervision 训练一个新的 compact cell
```

VPGS-SLAM 已经用了 online distillation 进行 submap fusion，这说明这个方向是合理的；但你可以把 distillation 的对象从整个 submap 降到 anchor cell。 

这就是你的差异：

```text
VPGS:
    submap-level online distillation

你:
    anchor-cell-level selective distillation
```

这会更细、更省、更可控。

---

## **5. 最有潜力的“特殊好 idea”：融合前的 Anchor Consensus**

我觉得你可以主打一个概念：

```text
Anchor Consensus before Gaussian Fusion
```

意思是：

在两个 submap 融合前，先在 anchor 层达成几何共识；只有通过 anchor consensus 的区域才允许 Gaussian cell 合并或复用。

可以定义三种 anchor 状态：

```text
1. confirmed anchor
   两个 submap 都支持，几何一致
   → merge anchors，merge / distill cells

2. conflicted anchor
   两个 submap 都看到，但位置/尺度/法向冲突
   → 暂不融合，进入 local deformation/refinement

3. novel anchor
   只有一个 submap 支持
   → 保留，但不用于 loop constraint
```

这样 submap fusion 不再是粗暴地：

```text
两个 submap 对齐后全都合并
```

而是：

```text
每个 anchor 区域单独判断：
    是否一致？
    是否冲突？
    是否重复？
    是否新区域？
```

这个非常适合处理 GS submap overlap 的常见问题。

---

## **6. 这个 anchor fusion 相比现有方法的理论优势**

你可以在论文里这样说：

### **对比 keyframe-centric fusion**

keyframe-centric：

```text
keyframe pose 是 fusion node
Gaussian 通过 keyframe ownership 被动更新
```

问题：

```text
keyframe 是相机状态，不是场景状态
一个 keyframe 可以看到很多不连续区域
同一个场景结构可以被多个 keyframe 重复拥有
```

anchor-centric：

```text
anchor 是场景状态
fusion 发生在 scene element 层
一个 anchor 对应局部几何区域
同一结构只应该有一个 persistent anchor
```

这比 keyframe ownership 更自然。

---

### **对比 Gaussian-level fusion**

Gaussian-level：

```text
primitive 太多
opacity/color/covariance 高自由度
很难判断两个 Gaussian 是否表示同一表面
```

anchor-level：

```text
node 更少
有时序支持
有几何语义：位置、法向、尺度、support、uncertainty
更适合做 correspondence 和 merge
```

所以 anchor 是一个更好的 fusion primitive。

---

### **对比 voxel-anchor fusion**

voxel anchor：

```text
由空间网格决定
规则、稳定，但可能和实际观测/几何成熟度无关
```

你的 certified anchor：

```text
由 temporal evidence + scale-aligned depth + uncertainty 决定
更接近 SLAM 里的可靠地图元素
```

这就是你和 VPGS 的区别。

---

## **7. 最小实验怎么验证这个 idea？**

你需要设计一组非常针对性的实验，不只是 PSNR。

### **实验 1：重复 Gaussian 抑制**

比较：

```text
keyframe-submap fusion
Gaussian-level prune
anchor-level duplicate suppression
```

指标：

```text
number of Gaussians
VRAM
PSNR / SSIM / LPIPS
floater count
overlap region rendering error
```

如果 anchor-level fusion 在相近 PSNR 下减少 Gaussian 数量，这个点就成立。

---

### **实验 2：submap overlap conflict**

选有 loop 或重访的序列，比较：

```text
before fusion
rigid submap alignment
anchor consensus fusion
```

指标：

```text
overlap depth discrepancy
rendered depth L1
normal disagreement
duplicate anchors / duplicate Gaussians
ATE / RPE
```

核心要证明：

```text
anchor consensus 能识别：
    confirmed / conflicted / novel 区域
```

---

### **实验 3：fusion timing**

比较三种策略：

```text
A. late Gaussian fusion
B. submap-level distillation
C. anchor-first fusion then cell distillation
```

指标：

```text
fusion time
memory peak
map size after fusion
PSNR loss after fusion
```

你应该期待：

```text
anchor-first fusion:
    peak memory 更低
    重复高斯更少
    fusion 后 PSNR loss 更小
```

---

## **8. 你当前最应该采用的设计**

我建议你的系统主线变成：

```text
Anchor-Centric Submap Fusion for Monocular Gaussian SLAM
```

关键模块：

```text
1. Anchor construction
   DPVO / patch witness + scale-aligned dense depth
   → mature anchors

2. Anchor-centric submap
   submap = connected mature anchors
   keyframes = observations only

3. Anchor consensus for fusion
   overlap detection
   anchor correspondence
   confirmed / conflicted / novel classification

4. Anchor-cell Gaussian representation
   each anchor owns compact Gaussian cell
   explicit first, Scaffold-like optional

5. Selective cell fusion / distillation
   only confirmed anchors merge cells
   conflicted anchors refine
   novel anchors remain separate
```

这比你原来“dual-submap soft fusion / candidate-path validation”的叙事更顶会一点，因为它从 **submap 路由问题** 提升到了 **map ownership 与 fusion primitive 问题**。

---

## **9. 最终判断**

现有研究大多这样做：

```text
LoopSplat:
    3DGS registration → loop edge → PGO → rigid submap alignment

GLC-SLAM:
    Gaussian submaps + loop closure + global optimization

VPGS-SLAM:
    voxel anchors + multiple submaps + 2D-3D Gaussian loop closure + online distillation

SING3R-SLAM:
    local submap reconstruction → global Gaussian map → differentiable refinement
```

它们都没有完全解决你关心的核心矛盾：

```text
keyframe/submap 作为管理单位太粗
Gaussian 作为 fusion primitive 又太细、太多、太自由
```

你的 anchor 结构正好在中间：

```text
keyframe 太观测侧
Gaussian 太渲染侧
anchor 是更合适的地图结构侧 fusion unit
```

所以最有价值的 idea 是：

**不要在 Gaussian 层融合 submaps，而是在 anchor 层先达成几何共识，再对 anchor-owned Gaussian cells 做选择性合并、重采样或蒸馏。**

这可以成为你顶会主线里的核心贡献之一。