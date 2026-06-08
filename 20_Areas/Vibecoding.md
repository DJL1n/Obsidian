# Vibecoding — AI 协作编程

> Vibe Coding（Andrej Karpathy 提出）指完全进入 "flow state" 让 AI 驱动代码实现，人类专注于高阶方向、设计判断和质量把控。

## 核心理念

```text
不是 "AI 代替你写代码"
而是 "你在一个更高的抽象层次编码"
人类：方向、设计、安全边界、验收
AI：实现、调试、重构、文档
```

---

## 工作流

### Vibe Coding 流程
```
大方向思考（人类）
  → 具象化 prompt / spec（人类 + AI）
  → AI 实现初版（AI）
  → 人类 review + 修正（人类）
  → 迭代循环
```

### 关键原则
1. **不要 micromanage** — 告诉 AI 做什么，不是怎么做
2. **分而治之** — 大任务拆成可验证的小块
3. **保持可回退** — git commit 频繁，每个小步都可回滚
4. **验证驱动** — 让 AI 写 test，你在 test 层面验收
5. **边界意识** — AI 不直接操作生产环境、不修改敏感配置

---

## Speaker Skill — AI 语音与表达

### 概述
Speaker skill 涵盖 AI 语音交互、语音合成、语音助手开发、以及 AI 辅助演讲/表达。

### 核心能力

#### 1. AI Text-to-Speech / 语音合成
- Hermes CLI 内置 `text_to_speech` 工具：直接文本转语音，用于消息通知、语音备忘录、演示辅助
- 支持的 provider：edge / openai / elevenlabs / MiniMax 等
- 用途：研究笔记语音版、论文朗读、会议提醒、播客脚本试听

#### 2. AI 语音助手 / 智能音箱技能
- 智能音箱（Alexa / Google Home / 小爱等）技能开发
- 语音交互设计原则：唤醒词 → 意图识别 → 槽位填充 → 响应生成
- Hermes gateway 可对接语音通道

#### 3. AI 辅助演讲与表达
- 论文答辩 / 汇报排练：AI 模拟听众提问
- 演讲稿润色：节奏、语气、过渡句优化
- 即兴表达练习：给定主题，AI 生成结构化回应框架

#### 4. 语音研究工具
- 音频分析 / 语谱图可视化
- 语音特征提取（mel spectrogram, MFCC, chroma）
- 语音合成质量评估（MOS, CER）

### Speaker Skill 在 Vibecoding 中的位置

```
Vibecoding workflow
  → 产出代码 / 论文 / 笔记
  → Speaker skill 将这些产出转化为语音形式
  → 用于：通勤听取、快速 review、演示辅助、多模态学习
```

### 相关工具
- `text_to_speech` (Hermes CLI) — 直接文本转语音
- `songsee` (Hermes skill) — 音频语谱图分析
- `songwriting-and-ai-music` — AI 音乐生成（可选扩展）

---

## 工具链

### Hermes Agent Skills
| Skill | 用途 | 评价 |
|---|---|---|
| `speaker` (text_to_speech) | 文本转语音 | 快速产出语音备忘录 |
| `claude-code` | 代码实现委托 | vibe coding 核心引擎 |
| `delegate_task` | 并行子任务 | 复杂任务拆分 |
| `cronjob` | 定时任务 | 每日摘要 / 自动检查 |

### 编码环境
- Mac + VS Code / terminal
- git (频繁 commit, 可回退)
- Python / C++ project structure

---

## 最佳实践（踩坑记录）

### Do
- 每个 prompt 前先想清楚：我要什么输出？
- 复杂任务先写 spec 再 delegate
- AI 写完代码后，diff review 再合并
- 用 test 验证 AI 输出

### Don't
- 不 review 就直接 merge
- 一次给 AI 太多无关上下文
- 让 AI 处理敏感凭据
- 期待 AI 一次写出完美代码

---

## 相关笔记
- [[60_WorkingStyle/prompt-engineering]]
- [[40_Knowledge/Methods/Agent-Delegation-Workflow]]

---

## 索引
- [[20_Areas/INDEX|返回 Areas 索引]]
