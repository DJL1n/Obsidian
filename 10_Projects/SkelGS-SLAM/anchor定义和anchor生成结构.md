可以。这里需要把三件事分开：

**DPVO / DPV-SLAM 的 patch graph 是 anchor 生成器，不是 anchor 本体。**  
**深度先验是 reserve evidence，不直接变成稳定 anchor。**  
**普通高斯的归属问题必须从 anchor 生成阶段就预留接口，否则后面建图会混乱。**

我建议最终采用：

**DPV-SLAM-style persistent patch graph + AnchorBank。**  
第一版工程实现可以挂在 DPVO 前端上，但数据结构要按 DPV-SLAM 的长期 patch graph / frame cache 思路设计，而不是按纯 DPVO sliding window 设计。

原因是 DPVO 本身是滑窗 VO，会移除滑窗外的 keyframes 和 features；DPV-SLAM 在 DPVO 的 patch graph 上加入 loop closure、long-range proximity factors 和 global BA，更接近“可持续信赖 anchor”需要的长期证据机制。DPVO 的核心是 sparse patch-based correspondence + differentiable BA；patch graph 中边连接 patch 与 frame，网络预测 2D trajectory update 和 confidence，再用 BA 更新 pose 与 patch inverse depth。  DPV-SLAM 则保留 DPVO 前端，并用 proximity loop closure / image retrieval loop closure 修正漂移；它还利用 DPVO patch representation 的方向性，把旧 patch 连接到近期 frame，以较小存储代价形成更长期的 patch graph。 

---

# **1. Anchor 的核心定义，可记录版**

## **1.1 一句话定义**

**Anchor 是由 DPVO / DPV-SLAM patch observations 与深度先验 reserve observations 共同支持的 compact 3D 几何原型。它不是 Gaussian，不是 Scaffold-GS anchor，也不是单个 DPVO patch；它是一个长期维护的局部几何单位，用来压缩多视角重复观测、度量几何稳定性，并在后续建图阶段负责普通高斯的出生、归属、合并、隔离和删除。**

更形式化地说：

a_i = \{ \mu_i,\ C_i,\ v_i^{geo},\ v_i^{app},\ M_i,\ \mathcal O_i,\ \Pi_i,\ s_i \}

其中最核心的是前五项：

a_i^{core} = \{ \mu_i,\ C_i,\ v_i^{geo},\ v_i^{app},\ M_i \}

---

## **1.2 每个量的含义**

### \mu_i**：anchor 的 3D 位置中心**

\mu_i \in \mathbb R^3

它不是某一帧的单点反投影，而是多帧、多 patch、多深度 reserve observation 融合后的局部几何中心。

对于 DPVO patch：

x_{ik} = G_i \pi^{-1}(u_{ik}, d_{ik})

其中 G_i 是 keyframe pose，u_{ik} 是 patch center，d_{ik} 是 DPVO / DPV-SLAM 优化后的 inverse depth。

---

### C_i**：局部几何张量**

C_i = \frac{1}{M_i} \sum_o w_{oi} (x_o-\mu_i)(x_o-\mu_i)^T

它表达 anchor 的局部几何形状，而不是额外手写一堆几何类别。

对 C_i 做特征值分解：

C_i = R_i \operatorname{diag}(\lambda_1,\lambda_2,\lambda_3) R_i^T, \quad \lambda_1 \ge \lambda_2 \ge \lambda_3

可以自然读出几何特性：

|**eigen-spectrum**|**几何意义**|**anchor 行为**|
|---|---|---|
|\lambda_3 \ll \lambda_2 \approx \lambda_1|局部平面 / surface patch|可作为 surface anchor|
|\lambda_3 \ll \lambda_2 \ll \lambda_1|边缘 / line-like structure|可作为 edge anchor|
|\lambda_1,\lambda_2,\lambda_3 都小|点状 / corner-like structure|可作为 compact point anchor|
|\lambda_3 长期较大|体状散乱 / 不稳定|不应稳定化|
|eigen-spectrum 随时间大幅变化|几何假设漂移|suppress 或 split|

如果是平面型 anchor，法向可以直接取最小特征值方向：

n_i = r_3

这比维护 `plane_prob / edge_prob / corner_prob` 更干净。

---

### v_i^{geo}**：几何方差**

v_i^{geo}

表示 anchor 的 3D 几何稳定性。它应综合：

1. DPVO / DPV-SLAM patch reprojection residual；
2. 同一 anchor 内 3D observations 的离散程度；
3. loop / revisit 后几何是否仍然一致；
4. 深度先验进入 anchor 后是否使几何方差升高。

一个简化写法：

v_i^{geo} = \operatorname{EMA} \left( \underbrace{ \|x_o-\hat{x}_{a_i}\|^2 }_{3D\ residual} + \alpha \underbrace{ \sum_j w_{ikj} \|\pi(G_j^{-1}x_o)-\mathcal I_{ikj}\|^2 }_{DPVO\ reprojection\ residual} \right)

其中 \mathcal I_{ikj} 是 DPVO 预测的 ideal reprojection。DPVO / DPV-SLAM 本身会为 patch graph edge 预测 confidence，并在 Mahalanobis BA objective 中加权这些误差，这正好可以作为 anchor 几何方差的来源。 

---

### v_i^{app}**：外观方差**

v_i^{app}

表示外观可解释性，而不是几何真实性。

这一项保留是必要的，因为镜面、高反射、透明、低纹理区域经常是：

v_i^{geo} \text{ 低或中等},\quad v_i^{app} \text{ 高}

也就是说，它们的物理表面可能稳定，但 appearance 不稳定。VarSplat 的启发在这里有价值：它把 variance 作为跨 tracking、registration、loop detection 的统一可靠性信号，而不是为每个模块手写规则。 

所以这里的原则是：

**几何可信和外观可信必须分离。**  
镜面 / 高反射不应因为 appearance variance 高就被禁止生成 anchor。  
动态物体则通常会导致 v^{geo} 也长期高，因此不能稳定化为静态 anchor。

---

### M_i**：effective multi-view support mass**

M_i

不是原始观测数。它必须抑制同视角重复刷分。

推荐定义：

M_i = \sum_b \min \left( 1, \sum_{o \in \text{view bin } b} w_{oi} \right)

也就是每个视角 bin 最多贡献有限 mass。这样相机停在一个位置抖动 100 帧，不会把 anchor 错误提升为长期可信。

这个量服务于“可持续信赖”：

M_i \uparrow \quad \text{并且} \quad v_i^{geo} \downarrow \text{ 或保持有界}

才说明多视角证据真的在收敛。

---

### \mathcal O_i**：anchor 的 evidence set**

\mathcal O_i = \mathcal O_i^{DPVO} \cup \mathcal O_i^{depth}

其中：

\mathcal O_i^{DPVO}

来自 DPVO / DPV-SLAM patch trajectories、inverse depths、edge confidences、reprojection residuals。

\mathcal O_i^{depth}

来自深度先验，但默认是 reserve evidence。

深度先验的角色不是“直接造 anchor”，而是：

**补充 DPVO 稀疏 patch 没覆盖到的几何候选，尤其是低纹理、反光、镜面、弱匹配但几何可重复的区域。**

---

### \Pi_i**：anchor 的 support / ownership domain**

\Pi_i

是 anchor 在 3D 中的管辖域。后面普通高斯的归属必须依赖它。

最简单形式：

\Pi_i = \left\{ x: (x-\mu_i)^T(C_i+\epsilon I)^{-1}(x-\mu_i) < \tau \right\}

如果是平面型 anchor，可以用：

\Pi_i = \left\{ x: |n_i^T(x-\mu_i)| < \tau_n, \quad \|P_i(x-\mu_i)\| < r_i \right\}

其中 P_i = I - n_i n_i^T。

这一步非常关键，因为它提前解决普通高斯归属问题。

---

### s_i**：anchor 状态**

不需要复杂状态机，第一版只保留四个状态：

```text
reserve     // 主要由深度先验支撑，尚未成为正式 anchor
candidate   // 有 DPVO/DPV-SLAM 或多视图支持，但还不稳定
stable      // 几何方差低，support mass 足够，形状稳定
suppressed  // 长期高方差、动态嫌疑、错误合并或证据矛盾
```

不要一开始引入太多类别。材料类型、动态类型、用途类型都先从 C_i, v^{geo}, v^{app}, M_i 派生。

---

# **2. 可持续信赖 anchor 的定义**

我建议写成：

**一个 anchor 是可持续信赖的，当多个时间窗口、多个视角、多个 DPVO patch trajectories 以及可兼容的 depth-prior reserve observations 能够收敛到同一个 compact 3D 几何原型，并且该原型的几何张量** C_i**、几何方差** v_i^{geo}**、外观方差** v_i^{app} **与 effective support mass** M_i **在时间上表现为稳定、可解释、非发散时，它才是 stable anchor。**

形式上：

\operatorname{Stable}(a_i) \Longleftrightarrow \begin{cases} M_i \ge M_{\min} \\ v_i^{geo} \le \tau_g \\ \Delta C_i \le \tau_C \\ \Delta \mu_i \le \tau_\mu \\ \text{view support is not degenerate} \\ \text{no persistent contradiction} \end{cases}

这里的重点不是阈值，而是趋势：

M_i \uparrow, \quad v_i^{geo} \downarrow \text{ or bounded}, \quad C_i \text{ stable}, \quad \text{multi-view observations fuse instead of conflict}

这和 AMB3R 提到的 compact 3D backend 思路一致：多视角情况下，多个 2D pixels 可能对应同一个 3D point，因此后端表示应该用 spatial compactness 约束这些 many-to-one observations，而不是让每个 pixel 都产生一个独立实体。 

---

# **3. 普通高斯归属问题：现在就要预留，不等建图阶段**

有准备，而且这里必须优化。后面普通高斯的归属不能靠“谁生成它就是谁的”这种一次性规则，否则 anchor split、merge、loop correction、depth-prior 修正时会出问题。

## **3.1 原则**

**每个普通 Gaussian 必须有 primary parent anchor；边界、多层、透明、薄结构处允许 soft ownership。**

后续建图时：

g_j.parent = a_i

但同时可以维护 top-K soft ownership：

p(a_i \mid g_j) = \frac{ \exp(-E_{ji}) }{ \sum_l \exp(-E_{jl}) }

其中：

E_{ji} = D_{geo}(g_j, a_i) + \lambda_v v_i^{geo} + \lambda_s D_{support}(g_j,\Pi_i)

第一版只需要 primary owner；但内部最好保留 soft score，方便后面 split / merge。

---

## **3.2 Gaussian 归属的接口，anchor 生成阶段就输出**

虽然目前不做高斯建图，但 anchor generator 应该输出：

```text
anchor_id
mu
C
normal
support_domain Pi
v_geo
v_app
mass
state
source_observation_ids
ownership_basin
spawn_budget_hint
```

其中 `ownership_basin` 就是 \Pi_i。

`spawn_budget_hint` 不生成 Gaussian，只给后面控制规模：

B_i \propto \frac{ \operatorname{area}(\Pi_i) }{ \sqrt{v_i^{geo}}+\epsilon }

几何稳定、面积大的 anchor 后续可以生成更多普通高斯；几何不稳定的 anchor 不生成普通高斯。

---

## **3.3 后续 Gaussian 归属规则**

后面建图阶段应遵守：

1. **普通 Gaussian 不从 raw depth prior 直接出生。**
2. **普通 Gaussian 只能从 stable anchor 的 support domain 中出生。**
3. **reserve / candidate anchor 只能产生临时或低权重候选，不产生普通长期 Gaussian。**
4. **anchor merge 时，children Gaussian 合并归属。**
5. **anchor split 时，children Gaussian 根据 soft ownership 重新分配。**
6. **anchor suppressed 时，其 children Gaussian 进入 quarantine，而不是立即删除。**
7. **loop correction 后，先更新 anchor，再更新其 children Gaussian。**

这可以避免后期出现“同一个空间区域有多个 anchor 抢同一批 Gaussian”的问题。

---

# **4. DPVO / DPV-SLAM 结构如何变成 anchor evidence**

DPVO patch 形式上是：

P_{ik} = (x,y,1,d)

其中 d 是 inverse depth。DPVO 用 patch graph 表示 patch 与 frame 的关系，edge 连接 patch 和 frame；patch 在多个 connected frames 中的重投影组成 trajectory。网络对每条 edge 预测 2D update 和 confidence，再通过 BA 优化 pose 和 inverse depth。 

对我们来说，一个 DPVO patch trajectory 不是 anchor，而是一个 observation source：

o_{ik}^{patch} = \{ x_{ik}, \bar r_{ik}, \bar w_{ik}, \mathcal V_{ik}, h_{ik}, source=i \}

其中：

x_{ik} = G_i \pi^{-1}(u_{ik}, d_{ik})

\bar r_{ik} = \frac{ \sum_j w_{ikj} \|\pi(G_j^{-1}x_{ik})-\mathcal I_{ikj}\|^2 }{ \sum_j w_{ikj} }

\bar w_{ik} = \frac{1}{|\mathcal E_{ik}|} \sum_j w_{ikj}

\mathcal V_{ik}

是这个 patch 被观察到的 view directions 集合。

深度先验 observation 则是：

o_{iu}^{depth} = \{ x_{iu}^{D}, \sigma_{iu}^{D}, n_{iu}^{D}, c_{iu}^{D}, reserve \}

其中：

x_{iu}^{D} = G_i \pi^{-1}(u, D_i(u))

它默认不进入 stable anchor，只进入 reserve pool。

---

# **5. 可行方案一：Patch-as-Anchor MVP**

这是最简单版本。

## **做法**

每个 DPVO patch trajectory 生成一个 candidate anchor：

a_{ik} \leftarrow P_{ik}

然后用其多帧 reprojection residual、edge confidence 和 inverse-depth 稳定性判断是否 stable。

流程：

```text
DPVO patch P_ik
    ↓
optimized inverse depth d_ik
    ↓
world point x_ik
    ↓
edge confidence / reprojection residual
    ↓
candidate anchor
    ↓
stable / suppressed
```

## **优点**

实现最容易，不需要改 DPVO 主结构。DPVO 已经提供 patch depth、pose、patch graph edge confidence 和 residual-like 信息。DPVO 默认只跟踪少量 sparse patches，每帧 64-96 个 keypoints 量级，因此计算量可控。 

## **问题**

这个方案不够好，原因有三个：

第一，DPVO patch 是随机采样的，DPVO / DPV-SLAM 论文也明确说它随机选 keypoints，而不是使用 repeatable detector 或 image gradients。  随机 patch 可以做 tracking，但不一定覆盖建图需要的几何区域。

第二，一个 patch trajectory 太局部，不能表达 surface / edge / corner 的几何张量。

第三，多个 patch 可能落在同一个真实 3D surface 上，如果不融合，会产生重复 anchor，后面普通高斯归属会爆炸。

结论：

**Patch-as-Anchor 只能作为 debug baseline，不适合作最终方案。**

---

# **6. 可行方案二：Patch-Fusion AnchorBank，推荐第一版**

这是我建议的主方案。

## **核心思想**

**DPVO / DPV-SLAM patch 是 observation；AnchorBank 才是真正的 anchor backend。**

流程：

```text
DPVO / DPV-SLAM patch graph
    ↓
patch observations with pose, inverse depth, confidence, residual
    ↓
spatial hash / local neighbor search
    ↓
observation-to-anchor attraction
    ↓
anchor update: mu, C, v_geo, v_app, M
    ↓
merge / split / suppress
    ↓
stable anchors
```

深度先验并行进入：

```text
depth prior
    ↓
reserve observations
    ↓
attach to compatible anchors
    ↓
or wait in reserve pool
    ↓
promote only after multi-view / patch support
```

---

## **6.1 Observation-to-anchor attraction**

对每个 observation o，在 spatial hash 中查找附近 anchors，计算兼容能量：

D(o,a_i) = \underbrace{ (x_o-\mu_i)^T(C_i+\sigma_o^2I)^{-1}(x_o-\mu_i) }_{3D\ compactness} + \alpha \underbrace{ \bar r_o^2 }_{DPVO\ reprojection} + \beta \underbrace{ D_n(o,a_i) }_{normal/layer\ compatibility}

然后软分配：

q_{oi} = \frac{ \exp(-D(o,a_i)) }{ \sum_j \exp(-D(o,a_j)) }

如果没有兼容 anchor，则放入 unexplained bucket。当某个 bucket 的 effective support 足够，才 birth 新 candidate anchor。

---

## **6.2 Anchor 更新**

M_i \leftarrow \sum_o q_{oi} w_o

\mu_i \leftarrow \frac{1}{M_i} \sum_o q_{oi}w_o x_o

C_i \leftarrow \frac{1}{M_i} \sum_o q_{oi}w_o (x_o-\mu_i)(x_o-\mu_i)^T

v_i^{geo} \leftarrow \operatorname{EMA} \left( \sum_o q_{oi}w_o D_{geo}(o,a_i) \right)

v_i^{app} \leftarrow \operatorname{EMA} \left( \sum_o q_{oi}w_o D_{app}(o,a_i) \right)

这里的 D_{app} 可以先用 patch feature residual、photometric residual、或者 feature correlation stability 的 proxy。第一版没有必要做复杂外观建模，只要能区分“几何稳定但外观不稳定”即可。

---

## **6.3 Anchor-to-anchor merge / repulsion**

你说的“吸引力 / 排斥力”是对的，但要落成两个机制：

### **observation-to-anchor attraction**

这个负责把多视图同一 3D 区域融合到同一个 anchor。

### **compatible-anchor merge / repulsion**

这个负责避免多个 anchors 重复解释同一块稳定几何。

两个 anchor a_i,a_j 如果：

\|\mu_i-\mu_j\| < r_i+r_j

|n_i^T n_j| > \tau_n

并且合并后：

v_{ij}^{geo} \le \max(v_i^{geo},v_j^{geo})+\epsilon

则 merge，而不是 repulse。

只有在两个 anchor 过近、几何兼容、但暂时不能安全合并时，才使用 repulsion / suppression。

不能无差别 repulsion。以下情况必须保留多 anchor：

1. depth discontinuity 两侧；
2. 薄物体正反面；
3. 桌面和桌腿；
4. 玻璃表面和后方真实物体；
5. 镜面表面和反射解释层；
6. 两个近距离但法向不同的真实表面。

---

## **6.4 深度先验作为 reserve 的规则**

深度先验不直接生成 stable anchor，而是进入：

```text
DepthReservePool
```

每个 reserve observation 只做三件事：

1. **补充已有 anchor：** 如果它落入某个 anchor 的 support domain，并且不会显著增加 v^{geo}，则低权重吸收。
2. **形成 reserve cluster：** 如果多个视角的 depth prior 在 3D 中一致，但 DPVO patch 暂时没有覆盖，则形成 reserve candidate。
3. **等待验证：** 没有多视图支持、没有 DPVO patch 支持、或者导致几何方差升高时，只保留，不升级。

可以写成：

\operatorname{PromoteDepthReserve}(R) \Longleftrightarrow \begin{cases} M_R^{view} \ge M_D \\ v_R^{geo} \le \tau_D \\ \text{compatible with DPVO/DPV-SLAM geometry} \\ \text{not increasing local contradiction} \end{cases}

这样镜面、高反射、低纹理区域仍然可以通过稳定几何被 anchor 覆盖，但动态、视角不足、单帧深度幻觉不会污染 anchor bank。

---

## **6.5 这个方案的优点**

它解决四个问题：

第一，多视图多个像素 / patches 对应同一 3D 点或 surface 时，会被 compact fusion 成一个 anchor。

第二，anchor 数量由稳定 3D 几何支持区域决定，而不是由 pixels、frames 或 DPVO patches 数量决定。

第三，深度先验不会直接污染地图，只作为 reserve evidence 参与竞争。

第四，普通高斯后续天然有归属域 \Pi_i，不会变成无主高斯云。

结论：

**这是当前最适合你项目的 anchor 生成 base。**

---

# **7. 可行方案三：DPV-SLAM Global AnchorBank，长期版本**

这个是方案二的增强版。

## **做法**

使用 DPV-SLAM 的长期 patch graph / loop correction 结果来更新 AnchorBank。

流程：

```text
DPV-SLAM frontend
    ↓
local patch graph
    ↓
proximity loop factors / global BA
    ↓
pose and patch depth correction
    ↓
AnchorBank re-integration
    ↓
stable anchors reinforced or suppressed
```

DPV-SLAM 的 proximity loop closure 会用预估几何检测 loop，并通过 global BA 更新 pose 和 depth；另一个 classical backend 用 image retrieval 与 pose graph optimization 修正 scale drift。  这对 anchor 很有价值，因为 anchor 的“可持续信赖”不应只来自局部滑窗，而应接受重访后的验证。

## **必须注意的实现点**

Anchor observation 不应该只存 world coordinate x。否则 loop closure 后 pose 修正了，旧 observation 坐标会过期。

每个 observation 应该存：

```text
source_frame_id
source_pixel_or_patch_id
inverse_depth
local ray
pose_version
world_point_cache
```

loop / global BA 后，重新计算：

x_o = G_i^{new}\pi^{-1}(u_o,d_o^{new})

然后再更新 anchor。

这一步很关键，否则 anchor 会和被修正后的轨迹不一致。

## **适用性**

这个方案更接近最终系统，但第一版工程代价较高。

结论：

**最终应走 DPV-SLAM Global AnchorBank，但第一版可以先实现 DPVO + AnchorBank，然后保持数据结构兼容 DPV-SLAM 的 global correction。**

---

# **8. 我建议采用的 base**

## **推荐 base**

**以 DPV-SLAM-style patch graph 为概念 base，以 DPVO frontend 为第一版工程入口。**

具体说：

```text
DPVO frontend
    + persistent AnchorBank
    + DepthReservePool
    + DPV-SLAM-compatible observation storage
```

第一阶段可以不启用完整 loop closure，但 observation 结构必须支持未来的 pose correction。

  

不要采用：

```text
pure DPVO sliding window → direct anchors
```

因为纯 DPVO 会丢掉滑窗外 features，不符合“可持续信赖”。

  

也不要采用：

```text
depth prior dense points → anchors
```

因为这会把深度先验的错误、幻觉、多层冲突直接变成地图结构，规模也会失控。

---

# **9. Anchor 生成阶段的实际 pipeline**

## **Step 0：运行 DPVO / DPV-SLAM 前端**

输入：

```text
RGB frames
camera intrinsics
optional depth prior
```

输出：

```text
poses G_i
patches P_ik
inverse depths d_ik
patch graph edges
edge confidence w_ikj
trajectory residual proxy
```

---

## **Step 1：生成 DPVO patch observations**

对每个 patch：

x_{ik}=G_i\pi^{-1}(u_{ik},d_{ik})

计算该 patch 的有效支持：

\omega_{ik} = f \left( \sum_j w_{ikj}, \bar r_{ik}, \text{view diversity} \right)

低 confidence、重投影残差高、或几何发散的 patch 不直接丢掉，而是作为 low-weight observation 进入候选池。

---

## **Step 2：生成 depth reserve observations**

对 depth prior 做稀疏采样，而不是全像素灌入。

采样策略建议：

```text
uniform spatial sampling
+ depth discontinuity sampling
+ low-texture region sampling
+ high-reflectance / low-matching-confidence region sampling
+ normal-diversity sampling
```

每个 depth observation 存：

```text
x_D
depth_uncertainty
normal_proxy
source_frame
reserve_weight
```

初始权重必须低于 DPVO patch evidence。

---

## **Step 3：observation-to-anchor assignment**

对每个 observation，在 spatial hash 中找邻近 anchors，计算：

D(o,a_i)

如果存在低能量 anchor：

```text
attach observation to anchor
```

否则：

```text
put into unexplained local bucket
```

bucket 累积到足够 multi-view support 后 birth candidate anchor。

---

## **Step 4：更新 anchor 几何**

更新：

```text
mu
C
v_geo
v_app
M
view support
source observation list
support domain Pi
```

从 C 派生：

```text
normal
radius
geometry type proxy
ownership basin
```

不需要手写很多类别。

---

## **Step 5：merge / split / suppress**

### **merge**

如果两个 anchors 解释同一块几何，合并后方差不升高，则 merge。

### **split**

如果一个 anchor 内部 support 多模态，或者 v^{geo} 长期高但可分成两个低方差子簇，则 split。

### **suppress**

如果长期：

M_i \text{ 不增长}

并且：

v_i^{geo} \text{ 高}

则 suppress。不要马上删除，因为它可能只是视角暂时不足。

---

## **Step 6：输出 stable anchors**

最终 anchor generator 输出：

```text
StableAnchorSet
CandidateAnchorSet
DepthReservePool
ObservationToAnchorAssignment
AnchorOwnershipDomains
```

目前不生成 Gaussian。

---

# **10. 最终建议的第一版实现结构**

```cpp
struct AnchorObservation {
    int source_frame_id;
    int source_patch_id;       // -1 for depth prior
    Vec2 pixel;
    float inv_depth;
    Vec3 world_point_cache;
    Vec3 view_dir;

    float reproj_residual;
    float confidence;
    float depth_uncertainty;

    enum Source {
        DPVO_PATCH,
        DEPTH_RESERVE
    } source;
};

struct Anchor {
    int id;

    Vec3 mu;
    Mat3 C;

    float v_geo;
    float v_app;
    float mass;

    // derived, but cached for efficiency
    Vec3 normal;
    float radius;

    // ownership / future Gaussian interface
    SupportDomain Pi;
    float spawn_budget_hint;

    // evidence
    vector<int> observation_ids;

    enum State {
        RESERVE,
        CANDIDATE,
        STABLE,
        SUPPRESSED
    } state;
};

struct AnchorBank {
    SpatialHash hash;
    vector<Anchor> anchors;
    vector<AnchorObservation> observations;
    DepthReservePool reserve_pool;
};
```

第一版最小化可以去掉 `v_app`，但我不建议。只保留一个 variance 会误伤镜面、高反射、低纹理区域。

---

# **11. 最终整理后的设计判断**

你的 anchor 生成不应该是：

```text
DPVO patch = anchor
```

也不应该是：

```text
depth prior point = anchor
```

而应该是：

```text
DPVO / DPV-SLAM patch graph
    → reliable sparse temporal observations

depth prior
    → reserve geometric observations

AnchorBank
    → compact 3D geometry anchors
```

最合适的 base 是：

  

**DPVO frontend + DPV-SLAM-style persistent AnchorBank。**

  

第一版可以只挂在 DPVO 上做 anchor generation；但 observation 必须保留 source frame、local ray、inverse depth 和 pose version，使它以后能接 DPV-SLAM 的 loop correction / global BA。

  

核心定义最终可以压成一句：

  

**Anchor 是一个 compact 3D geometry unit。它融合 DPVO / DPV-SLAM patch trajectories 与 depth-prior reserve observations，用** \mu **表示位置，用** C **表示局部几何形状，用** v^{geo}, v^{app} **表示几何与外观不确定性，用** M **表示有效多视图支持，并通过 support domain** \Pi **为后续普通高斯提供唯一或软性的归属域。Stable anchor 的判据不是短期匹配质量，而是多视角、多时间、多来源观测是否在同一个局部几何原型上持续收敛。**