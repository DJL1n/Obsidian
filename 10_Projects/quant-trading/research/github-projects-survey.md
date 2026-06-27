# GitHub 项目调研

> 调研时间：2026-06-27
> 目的：为量化交易系统第一阶段搭建做准备，评估各项目的实用性

---

## 一、数据源

### 1. AKShare
- **仓库**: [akfamily/akshare](https://github.com/akfamily/akshare)
- **Star**: 20.7k | Fork: 3.3k
- **最后更新**: 1个月前（活跃维护）
- **License**: MIT
- **核心功能**:
  - 覆盖 A 股、港股、期货、期权、基金、外汇、加密货币等全市场数据
  - 支持宏观经济数据（CPI、PMI、GDP 等）
  - 提供实时行情、历史 K 线、龙虎榜、融资融券、北向资金等
  - 1500+ API 接口，持续扩展中
- **安装**: `pip install akshare`
- **数据接口**:
  - 返回 `pandas.DataFrame` 格式
  - 无需 API Key，直接调用（部分接口有频率限制）
  - 函数式 API：`ak.stock_zh_a_hist(symbol="000001", period="daily", ...)`
- **回测能力**: 无（纯数据获取库）
- **文档质量**: ⭐⭐⭐⭐⭐ — 文档完整，API 参考详细，中文文档齐全
- **适合阶段**: 第一阶段（核心数据源）
- **推荐度**: **高**
- **理由**:
  - 活跃维护，社区强大
  - 无需注册/Token，开箱即用
  - 覆盖国内全市场数据，API 设计简单直观
  - 返回 pandas DataFrame，与量化生态无缝对接
  - 注意：部分接口有反爬限制，建议加 delay

### 2. Tushare
- **仓库**: [waditu/tushare](https://github.com/waditu/tushare)
- **Star**: 15.2k | Fork: 4.4k
- **最后更新**: 6年前（基本停更）
- **License**: MIT
- **核心功能**:
  - A 股历史数据（日/周/月 K 线）
  - 基本面数据（财务指标、分红送转等）
  - 龙虎榜、资金流向等
- **安装**: `pip install tushare`（原版） / `pip install tushare-pro`（Pro 版）
- **数据接口**:
  - 需要注册获取 API Token
  - 返回 `pandas.DataFrame`
  - 函数式 API：`pro.query('daily', ts_code='000001.SZ', ...)`
- **回测能力**: 无（纯数据获取库）
- **文档质量**: ⭐⭐⭐ — 文档基本完整但已过期，Pro 版文档在官网 tushare.pro
- **适合阶段**: 不推荐作为主要数据源
- **推荐度**: **低**
- **理由**:
  - GitHub 仓库已 6 年未更新，处于停更状态
  - 新版 Tushare Pro（tushare.pro）数据有积分限制，免费额度有限
  - Token 获取需要实名认证
  - AKShare 在功能覆盖和易用性上已超越 Tushare
  - 仅在需要 Tushare 特有接口时作为补充

### 3. yfinance
- **仓库**: [ranaroussi/yfinance](https://github.com/ranaroussi/yfinance)
- **Star**: 24.4k | Fork: 3.3k
- **最后更新**: 1个月前（活跃维护）
- **License**: Apache-2.0
- **核心功能**:
  - 从 Yahoo Finance 下载全球市场数据
  - 支持股票、ETF、基金、外汇、加密货币
  - 提供 OHLCV 数据、基本面数据、新闻等
- **安装**: `pip install yfinance`
- **数据接口**:
  - 无需 API Key
  - 返回 `pandas.DataFrame`
  - 面向对象 API：`yf.Ticker('AAPL').history(period='1y')`
  - 批量下载：`yf.download(['AAPL', 'MSFT'], period='1y')`
- **回测能力**: 无（纯数据获取库）
- **文档质量**: ⭐⭐⭐⭐ — 英文文档，README 和示例清晰
- **适合阶段**: 第一阶段（美股/全球数据）
- **推荐度**: **高**
- **理由**:
  - Yahoo Finance 数据免费、覆盖面广
  - 维护活跃，社区支持好
  - API 简洁，pandas 集成好
  - A 股数据质量和覆盖率不如 AKShare，适合美股/全球市场
  - 注意：Yahoo Finance 可能偶尔不稳定

### 4. OpenBB
- **仓库**: [OpenBB-finance/OpenBB](https://github.com/OpenBB-finance/OpenBB)
- **Star**: 69.7k | Fork: 7.1k
- **最后更新**: 2 天前（非常活跃）
- **License**: AGPL-3.0
- **核心功能**:
  - 统一金融数据平台，整合多个数据源
  - 支持股票、期货、期权、加密货币、外汇、宏观经济
  - 提供 CLI、API、Desktop App、Jupyter 等多种使用方式
  - 内置数据可视化、分析工具
  - 支持 AI Agent 集成
- **安装**: `pip install openbb`（CLI） / `pip install openbb[all]`（完整版）
- **数据接口**:
  - 统一 API 层：`obb.equity.price.history('AAPL', ...)`
  - 后端支持：Yahoo Finance、Finnhub、Polygon、CoinGecko 等 30+ 数据源
  - 返回 `pandas.DataFrame` 或自定义对象
  - 部分数据源需要 API Key
- **回测能力**: 有内置回测模块（基础版），更复杂回测需搭配其他框架
- **文档质量**: ⭐⭐⭐⭐⭐ — 文档体系完整，在线文档、教程、示例丰富
- **适合阶段**: 第二阶段（统一数据层）
- **推荐度**: **高**
- **理由**:
  - Star 数最高，生态最成熟
  - 统一接口抽象，可切换数据源
  - 社区和商业化支持强（OpenBB Finance Inc.）
  - AGPL 许可证需注意（商业使用可能有限制）
  - 学习曲线较陡，适合中后期作为统一数据平台
  - 可作为长期战略选择，第一阶段先用 AKShare + yfinance

---

## 二、回测框架

### 5. vectorbt
- **仓库**: [polakowo/vectorbt](https://github.com/polakowo/vectorbt)
- **Star**: 8.1k | Fork: 1k
- **最后更新**: 2 周前（活跃维护）
- **License**: Apache-2.0
- **核心功能**:
  - 基于 NumPy/Numba 的向量化回测引擎
  - 支持多参数组合、蒙特卡洛模拟
  - 内置技术指标计算、Portfolio 分析
  - 支持多种策略：动量、均线交叉、布林带等
  - 可并行运行成千上万次回测
- **安装**: `pip install vectorbt`（核心版）/ `pip install vectorbt-pro`（Pro 版，闭源）
- **数据接口**:
  - 接受 `pandas.DataFrame` / `numpy.ndarray` / OHLCV 数据
  - `Data` 对象封装：`data = vbt.Data.from_df(df)`
  - 支持多资产、多频率数据
- **回测能力**: ⭐⭐⭐⭐⭐
  - 快速回测（向量化，适合日内/日线策略）
  - 支持参数优化、Walk-Forward Analysis
  - 输出详细的绩效指标：Sharpe、Sortino、最大回撤等
  - **不支持**：事件驱动回测、滑点/手续费精细建模
- **文档质量**: ⭐⭐⭐⭐⭐ — 文档极其优秀，示例丰富，教程完整
- **适合阶段**: 第一阶段（核心回测引擎）
- **推荐度**: **高**
- **理由**:
  - 速度极快（Numba 加速），适合快速验证策略想法
  - API 设计优雅，与 pandas 生态深度集成
  - 文档和示例质量高，上手快
  - 适合用户画像（SLAM 背景，熟悉编程）
  - 缺点：不做事件驱动回测，复杂策略需 backtrader

### 6. backtrader
- **仓库**: [mementum/backtrader](https://github.com/mementum/backtrader)
- **Star**: 22.1k | Fork: 5.2k
- **最后更新**: 3 年前（基本停更）
- **License**: GPL-3.0
- **核心功能**:
  - 事件驱动回测框架
  - 支持多数据源、多策略、多 broker 模拟
  - 内置丰富的技术指标和绘图功能
  - 支持实时交易（对接 Interactive Brokers 等）
  - 灵活的 Cerebro 引擎架构
- **安装**: `pip install backtrader`
- **数据接口**:
  - 内置 CSV 数据加载器
  - 支持自定义 Data Feed
  - 可对接 Yahoo Finance、Visual Chart 等
  - 数据格式：OHLCV + 可选成交量
- **回测能力**: ⭐⭐⭐⭐⭐
  - 完整的事件驱动回测
  - 支持滑点、手续费、仓位管理
  - 多品种、多时间框架回测
  - 支持 Paper Trading（模拟交易）
- **文档质量**: ⭐⭐⭐⭐ — 文档详细但有些地方过时，社区示例丰富
- **适合阶段**: 第一阶段（复杂策略回测）
- **推荐度**: **中**
- **理由**:
  - 功能最全面的事件驱动回测框架
  - Star 数高，社区活跃（虽然官方停更）
  - API 设计较为复杂，学习曲线陡峭
  - GPL 许可证限制商业用途
  - 3 年未更新，与新版 pandas/Python 可能有兼容问题
  - 建议作为 vectorbt 的补充（处理需要事件驱动的复杂策略）

### 7. Zipline
- **仓库**: [quantopian/zipline](https://github.com/quantopian/zipline)
- **Star**: 19.9k | Fork: 5k
- **最后更新**: 6 年前（已停更）
- **License**: Apache-2.0
- **核心功能**:
  - Quantopian 开发的经典回测框架
  - 类似 Pythonic API，适合编写策略
  - 支持美股市场（基于 Bundle 系统）
  - 内置 pyfolio 风格的绩效分析
- **安装**: `pip install zipline`（安装复杂，依赖多，需 conda）
- **数据接口**:
  - Bundle 系统：`zipline ingest -b bundle_name`
  - 支持 Quandl、Yahoo Finance 等数据源
  - 返回自定义 BarData 对象
- **回测能力**: ⭐⭐⭐
  - 事件驱动回测
  - 仅限美股（原版）
  - 安装和配置复杂
  - 社区有 zipline-reloaded 等 fork，但也不活跃
- **文档质量**: ⭐⭐⭐ — 文档存在但已过期，Quantopian 平台关闭后维护不足
- **适合阶段**: 不推荐
- **推荐度**: **低**
- **理由**:
  - Quantopian 平台已于 2020 年关闭，项目基本死亡
  - 6 年未更新，兼容性极差
  - 仅支持美股，A 股支持薄弱
  - 安装复杂（需要 conda + 大量依赖）
  - 建议直接使用 vectorbt + backtrader 替代

### 8. vn.py
- **仓库**: [vnpy/vnpy](https://github.com/vnpy/vnpy)
- **Star**: 42.1k | Fork: 12k
- **最后更新**: 1个月前（活跃维护）
- **License**: MIT
- **核心功能**:
  - 完整的 Python 量化交易框架
  - 支持回测、模拟交易、实盘交易
  - 对接国内期货、股票、期权市场
  - 支持 CTP、XTP、恒生等交易接口
  - 提供 GUI 交易界面（vnpy Trader）
- **安装**: `pip install vnpy`（核心）/ `pip install vnpy-ctrader`（完整版）
- **数据接口**:
  - 内置数据下载（TQ 数据、本地 CSV）
  - 支持实时行情推送
  - Tick 级数据支持
  - 数据格式：BarData、TickData 等自定义对象
- **回测能力**: ⭐⭐⭐⭐
  - 基于事件驱动的回测引擎
  - 支持分钟/日/ tick 级别回测
  - 可模拟交易，对接实盘
  - **不支持**：向量化批量回测（速度较慢）
- **文档质量**: ⭐⭐⭐ — 中文文档为主，文档体系较完整但更新不及时
- **适合阶段**: 第二阶段（实盘交易）
- **推荐度**: **中**
- **理由**:
  - 国内最成熟的量化交易框架
  - Star 数高，社区活跃，中文资源丰富
  - 适合实盘交易对接（CTP、期货）
  - 学习曲线陡峭，API 设计较为传统
  - 回测性能不如 vectorbt，代码可读性一般
  - 第一阶段建议先用 vectorbt 做研究，vn.py 留到实盘阶段

---

## 三、AI 量化

### 9. TradingAgents
- **仓库**: [TauricResearch/TradingAgents](https://github.com/TauricResearch/TradingAgents)
- **Star**: 89k | Fork: 17.2k
- **最后更新**: 5 天前（非常活跃）
- **License**: Apache-2.0
- **核心功能**:
  - 多 Agent LLM 金融交易框架
  - 基于多个 AI Agent 协作进行金融分析和交易决策
  - 支持多种 LLM 后端（OpenAI、DeepSeek、Qwen、Claude 等）
  - 自动报告生成、市场分析、交易信号
  - CLI 和 Docker 部署支持
- **安装**: `pip install tradingagents` / `git clone` 后安装
- **数据接口**:
  - 内置 Yahoo Finance 数据源
  - 支持自定义数据源
  - 返回结构化的分析报告和交易信号
- **回测能力**: ⭐⭐ — 主要是分析和决策框架，非传统回测引擎
  - 提供交易信号和决策输出
  - 回测需要搭配其他框架
- **文档质量**: ⭐⭐⭐⭐ — README 详细，示例和快速开始指南完善
- **适合阶段**: 第三阶段（AI 交易决策）
- **推荐度**: **中**
- **理由**:
  - Star 数极高（89k），热度很高
  - 多 Agent 架构新颖，适合研究性探索
  - 需要 LLM API Key（成本较高）
  - 主要是研究/实验性质，非生产级
  - LLM 输出不可控，不适合直接用于实盘
  - A 股衍生版（TradingAgents-astock）已适配国内数据

### 10. FinRL
- **仓库**: [AI4Finance-Foundation/FinRL](https://github.com/AI4Finance-Foundation/FinRL)
- **Star**: 15.5k | Fork: 3.4k
- **最后更新**: 2 个月前（维护中）
- **License**: MIT
- **核心功能**:
  - 金融强化学习（RL）框架
  - 支持多种 RL 算法（PPO、A2C、DDPG 等）
  - 内置环境：股票交易、期权交易、组合管理
  - 支持 GPU 加速训练
  - 学术论文驱动（多篇 NeurIPS/AAAI 论文）
- **安装**: `pip install finrl`（基础版）/ Docker 部署推荐
- **数据接口**:
  - 内置 Yahoo Finance 下载器
  - 支持 CSV、自定义数据源
  - 数据格式：OHLCV + 技术指标作为状态空间
- **回测能力**: ⭐⭐⭐⭐
  - 基于 gymnasium 的强化学习环境
  - 训练后自动回测
  - 支持多品种、多资产组合优化
  - 绩效分析集成
- **文档质量**: ⭐⭐⭐⭐ — 文档体系完整，Jupyter 示例丰富，学术论文配套
- **适合阶段**: 第三阶段（强化学习探索）
- **推荐度**: **中**
- **理由**:
  - 学术界影响力大（AI4Finance 基金会支持）
  - 强化学习交易属于前沿研究方向
  - 训练成本高，需要大量数据和计算资源
  - RL 策略的泛化性和稳定性是长期挑战
  - 适合研究探索，不适合短期实盘
  - 与用户的 SLAM/ML 背景契合度高

### 11. Qlib
- **仓库**: [microsoft/qlib](https://github.com/microsoft/qlib)
- **Star**: 45.2k | Fork: 7.2k
- **最后更新**: 2 个月前（活跃维护）
- **License**: MIT
- **核心功能**:
  - AI 量化投资平台（微软出品）
  - 支持多种 ML 范式：监督学习、时序建模、强化学习
  - 内置数据管理、特征工程、模型训练pipeline
  - 支持因子挖掘、组合优化、Alpha 策略
  - 集成 RD-Agent 实现自动化 R&D
- **安装**: `pip install pyqlib` / 源码安装（推荐）
- **数据接口**:
  - 内置数据下载：`qlib init`（支持 A 股、美股）
  - 自定义数据格式（bin 文件格式，高性能）
  - 支持 CSV、数据库等数据导入
  - 数据格式：标准化 OHLCV + 自定义因子
- **回测能力**: ⭐⭐⭐⭐
  - 内置回测引擎
  - 支持因子回测、模型回测
  - 多种绩效评估指标
  - 组合优化和风险管理
- **文档质量**: ⭐⭐⭐⭐ — 文档系统完善，有详细教程和示例，中英文双语
- **适合阶段**: 第二阶段（AI 量化研究）
- **推荐度**: **高**
- **理由**:
  - 微软背书，长期维护有保障
  - 设计最完善的 AI 量化平台
  - 支持 A 股数据，与国内生态兼容好
  - 架构设计优秀，可扩展性强
  - 学习曲线中等，适合有 ML 背景的用户
  - 可作为核心 AI 量化研究平台

---

## 四、综合对比

### 数据源对比

| 项目 | Star | 活跃度 | A股 | 美股 | 免费 | 安装难度 | 推荐度 |
|------|------|--------|-----|------|------|----------|--------|
| AKShare | 20.7k | ⭐⭐⭐⭐⭐ | ✅ 全 | ✅ 基础 | ✅ | ⭐ | **高** |
| yfinance | 24.4k | ⭐⭐⭐⭐⭐ | ❌ 弱 | ✅ 全 | ✅ | ⭐ | **高** |
| Tushare | 15.2k | ⭐ | ✅ 全 | ❌ | ⚠️ 积分制 | ⭐ | **低** |
| OpenBB | 69.7k | ⭐⭐⭐⭐⭐ | ✅ | ✅ 全 | ⚠️ 部分付费 | ⭐⭐⭐ | **高** |

### 回测框架对比

| 项目 | Star | 活跃度 | 回测类型 | 速度 | 复杂度 | A股支持 | 推荐度 |
|------|------|--------|----------|------|--------|---------|--------|
| vectorbt | 8.1k | ⭐⭐⭐⭐⭐ | 向量化 | ⭐⭐⭐⭐⭐ | ⭐⭐ | ✅ | **高** |
| backtrader | 22.1k | ⭐ | 事件驱动 | ⭐⭐ | ⭐⭐⭐⭐ | ✅ | **中** |
| Zipline | 19.9k | ❌ | 事件驱动 | ⭐⭐ | ⭐⭐⭐⭐⭐ | ❌ | **低** |
| vn.py | 42.1k | ⭐⭐⭐⭐⭐ | 事件驱动 | ⭐⭐ | ⭐⭐⭐⭐ | ✅ 全 | **中** |

### AI 量化对比

| 项目 | Star | 活跃度 | AI类型 | 成熟度 | 计算需求 | 推荐度 |
|------|------|--------|--------|--------|----------|--------|
| TradingAgents | 89k | ⭐⭐⭐⭐⭐ | LLM Agent | ⭐⭐ | 低（需 API） | **中** |
| FinRL | 15.5k | ⭐⭐⭐⭐ | 强化学习 | ⭐⭐⭐ | ⭐⭐⭐⭐ | **中** |
| Qlib | 45.2k | ⭐⭐⭐⭐⭐ | ML/RL | ⭐⭐⭐⭐ | ⭐⭐⭐ | **高** |

---

## 五、第一阶段推荐方案

### 核心选型（Phase 1）

```
数据层：AKShare（A股/国内） + yfinance（美股/全球）
回测层：vectorbt（快速验证） + backtrader（复杂策略）
AI层：Qlib（长期研究平台）
```

### 安装清单

```bash
# 数据源
pip install akshare yfinance

# 回测
pip install vectorbt

# 基础工具
pip install pandas numpy matplotlib scipy scikit-learn
```

### 理由

1. **AKShare** 作为主要数据源：免费、无注册、覆盖全面、活跃维护
2. **yfinance** 作为全球数据补充：覆盖美股、ETF、加密货币
3. **vectorbt** 作为核心回测引擎：速度快、文档好、pandas 集成
4. **Qlib** 作为 AI 量化研究平台：微软支持、架构完善、长期价值高
5. **backtrader** 作为备选：仅当需要事件驱动回测时使用
6. **TradingAgents** 和 **FinRL** 暂不引入：成熟度和实用性需要更多验证

### 后续演进路线

- **Phase 2**: 引入 OpenBB 统一数据层 + vn.py 实盘交易
- **Phase 3**: 引入 FinRL（RL 交易）+ TradingAgents（LLM 决策）做前沿探索

---

*调研完成，所有数据来自 GitHub 实时页面，截至 2026-06-27。*
