# Vibecoding 概述 — AI 协作编程工作流

> Vibe Coding（Andrej Karpathy 提出）指完全进入 "flow state"，让 AI 驱动代码实现，人类专注于高阶方向、设计判断和质量把控。

## 核心理念

不是 "AI 代替你写代码"，而是 "你在一个更高的抽象层次编码"。

| 角色 | 职责 |
|------|------|
| 人类 | 方向、设计、安全边界、验收 |
| AI | 实现、调试、重构、文档 |

## 工作流

```
大方向思考（人类）
  → 具象化 prompt / spec（人类 + AI）
  → AI 实现初版（AI）
  → 人类 review + 修正（人类）
  → 迭代循环
```

## 关键原则

1. **不要 micromanage** — 告诉 AI 做什么，不是怎么做
2. **分而治之** — 大任务拆成可验证的小块
3. **保持可回退** — git commit 频繁
4. **验证驱动** — 让 AI 写 test，你在 test 层面验收
5. **边界意识** — AI 不直接操作生产环境、不修改敏感配置

## 当前聚焦

- [[Speaker-Skill|Speaker Skill]] — AI 辅助演讲技能
- [[Presentation-Skills|Presentation Skills]] — 演示处理技能集

## 相关笔记

- [[60_WorkingStyle/prompt-engineering]]
- [[40_Knowledge/Methods/Agent-Delegation-Workflow]]
- [[20_Areas/INDEX|返回 Areas 索引]]
