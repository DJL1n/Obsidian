# Codex Job: 最小可审计量化骨架

## 目标
建立最小可审计的日频回测 + 模拟交易骨架。**不要追求收益，不要接真实券商，不要接 API key。**

## 严格限制
1. 不允许接入真实交易账户
2. 不允许保存、读取或生成任何券商 API key
3. 不允许自动下单
4. 不允许使用杠杆、做空、期权、期货
5. 不要把新闻情绪作为交易信号
6. 不要引入 Qlib、OpenBB、TradingAgents 作为主依赖
7. 可以使用 pandas、numpy、sqlite、pytest
8. vectorbt 可以作为可选依赖，但第一版必须保留简单、可读、可测试的自建回测逻辑
9. 所有修改必须有测试
10. 输出必须说明修改文件、测试结果、剩余风险

## 任务清单

### A. 目录结构（已有，确认）
```
data/raw/
data/processed/
configs/
strategies/
factors/
backtests/
paper_trading/
reports/
docs/
tests/
```

### B. 配置文件
- `configs/universe.yaml` — 标的池（先用沪深300成分股列表）
- `configs/strategy_momentum_lowvol.yaml` — 动量+低波动策略参数
- `configs/risk_policy.yaml` — 风控参数（单股10%、行业30%、回撤5%）

### C. 文档
- `docs/DATA_POLICY.md` — 数据政策（来源、缓存、校验、缺失处理）
- `docs/BACKTEST_AUDIT_CHECKLIST.md` — 回测审计清单（未来函数、幸存者偏差、复权、成本）
- `docs/PAPER_TRADING_RULES.md` — 模拟交易规则（订单生命周期、成交逻辑、拒单）

### D. 最小数据接口
- 支持从本地 CSV 或 parquet 读取 OHLCV
- **暂时不强绑定 AKShare**（预留 adapter 接口）
- 数据字段：date, symbol, open, high, low, close, volume
- 数据完整性检查函数

### E. 最小因子
- momentum：过去 N 日收益（保留 signal_date）
- volatility：过去 N 日收益率标准差（保留 signal_date）
- 严禁使用 signal_date 之后的数据

### F. 最小回测
- 月度调仓
- signal_date 之后的下一个交易日成交
- 手续费和滑点
- 输出：每日净值、累计收益、最大回撤、换手率

### G. 模拟交易账本（sqlite）
- orders 表（order_id, strategy_id, symbol, side, target_weight, signal_date, status）
- trades 表（trade_id, order_id, symbol, side, quantity, price, commission, slippage）
- positions 表（date, symbol, quantity, cost_basis, market_price, market_value）
- portfolio 表（date, cash, market_value, total_equity, daily_return, drawdown）

### H. 测试（必须有）
- `test_no_lookahead.py` — 因子不能使用前日数据
- `test_trade_date_alignment.py` — 信号日和成交日不能相同
- `test_costs_applied.py` — 手续费和滑点进入收益
- `test_paper_ledger.py` — 订单、成交、持仓、净值能对应

## 运行
```bash
cd ~/code/quant-research
python -m pytest tests -v
git diff --check
```

## 输出要求
1. 文件变更列表
2. 测试结果
3. 当前系统能做什么
4. 当前系统不能做什么
5. 下一步建议
