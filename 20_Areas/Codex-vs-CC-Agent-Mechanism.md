# Codex CLI vs Claude Code 底层 Agent 机制对比

## 核心差异：架构哲学

### Codex CLI：模式化 Agent 系统
- **4 种独立模式**：Plan → Agent → Debug → Test
- 每种模式有**独立的 system prompt**，控制工具使用范围
- 模式切换由用户显式触发（`--mode plan` 等）
- **内部工具集隔离**：Test 模式只能调用测试相关工具，不能改代码

### Claude Code：统一 Agent 系统
- **单一 agent 核心**，行为由 system prompt 控制
- 没有硬编码的"模式"，通过 prompt engineering 切换行为
- **工具集统一**，所有工具都可用，通过权限控制

---

## 工具调用管道

### Codex CLI
- 基于 OpenAI Function Calling 的**结构化输出**
- 每个 tool call 有**超时和重试机制**
- 错误恢复：tool 失败 → 自动重试 → 降级策略
- **工具权限分级**：不同模式下可访问的工具子集不同

### Claude Code
- 基于 Anthropic Tool Use（结构化输出）
- **更细粒度的工具权限**：`--allow-tool` / `--deny-tool`
- **工具执行超时**可配置
- **错误恢复**：tool 失败 → 分析原因 → 尝试替代方案

---

## 状态管理

### Codex CLI
- **隐式记忆**：通过 conversation history 保持上下文
- **显式工具状态**：每个 tool call 的结果会反馈到下一次 decision
- **文件追踪**：记录修改过的文件，用于 diff 展示

### Claude Code
- **隐式记忆**：conversation history
- **文件变更追踪**：通过 `--diff` 展示修改
- **会话持久化**：通过 `--resume` 恢复中断会话

---

## 并发模型

### Codex CLI
- 单线程，sequential execution
- 每个 tool call → LLM 决策 → 下一个 tool call
- 无 subagent 机制

### Claude Code
- **支持 subagent 并发**（通过 `delegate_task` 或内部 fork）
- 每个 subagent 有**独立工具会话**
- 通过 Orchestrator 协调结果聚合

---

## 决策机制

### Codex CLI
- **模式驱动决策**：当前模式决定可用工具和行为边界
- **角色内聚**：每个模式只关心一类任务
- **状态机**：模式间转换有明确路径

### Claude Code
- **Prompt 驱动决策**：system prompt 决定行为边界
- **工具平等**：所有工具理论上都可用
- **灵活路由**：根据任务类型动态调整策略

---

## 你的场景适用性

### 适合 Codex 模式化的场景
- 需要**明确行为边界**（比如"只做测试"、"只做调试"）
- 需要**角色内聚**（每个模式专注一类任务）
- 需要**防止越界操作**（模式限制防止误操作）

### 适合 CC 统一机制的场景
- 需要**灵活切换**，通过 prompt 控制
- 需要**并行探索**（subagent 支持）
- 需要**细粒度权限控制**（`--allow-tool` 等）

---

## 补充说明

- Codex 的 4 种模式本质上是**状态机**，每个状态有独立的 tool set
- CC 的单一 agent 本质上是**策略模式**，通过 prompt 切换行为策略
- Codex 的并发限制是**架构设计选择**（简化错误处理）
- CC 的 subagent 支持是**可扩展性选择**（适合复杂任务分解）

---

## 参考

- Codex CLI 文档：https://code.visualstudio.com/docs/ai/codex-cli
- Claude Code 文档：https://docs.anthropic.com/en/docs/claude-code/overview
- 内部笔记：SkelGS-SLAM 项目中使用 CC 做代码实现的流程

---

*创建时间：2026-06-16*
*最后更新：2026-06-16*
