# Codex Job Log

## 已完成

### Job 0001: 最小可审计骨架
- **状态**: ✅ 完成
- **测试**: 24/24 通过
- **文件**: 22 文件，1825 行
- **Commit**: 8b4895c

### Job 0002: AKShare 数据源 Adapter
- **状态**: ✅ 完成
- **测试**: 29/29 通过（新增 5 个）
- **文件**: data/akshare_adapter.py, data/loader.py（修改）, tests/test_akshare_adapter.py
- **功能**: fetch_a_stock_hist()、24h parquet 缓存、1-3s rate limit、重试 3 次、字段标准化
- **Commit**: 6083f04

## 进行中

### Job 0003: 首次真实回测
- **状态**: ⏳ 运行中
- **任务**: 拉沪深 300 成分股 10 只 x 1 年数据，跑 momentum+lowvol 月度回测
- **输出**: reports/first_backtest/（净值、交易、摘要）
