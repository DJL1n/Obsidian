# Reflexion: An Agent that Reflects while Acting

> arXiv:2309.10697 | 2023

## 核心贡献
提出基于语言反思的元认知强化学习，让LLM智能体通过自我反思持续改进。

## 方法
Reflexion是一种让大语言模型智能体在任务环境中进行在线学习和自我改进的框架。核心理念是让智能体在执行任务后进行自然语言反思：当任务失败时，智能体生成反思文本（反思为什么会失败），将反思存储到记忆中；下次遇到类似情况时，智能体会回顾这些反思并调整策略。整个框架不修改系统提示或重新训练模型，而是通过 episodic memory（情景记忆）实现经验积累。在WebShop（电商搜索）和AlfWorld（文本冒险游戏）等benchmark上，Reflexion显著超越了零样本LLM和ReAct方法，展现出类似元认知强化学习的在线学习能力。反思机制本质上是一种无模型强化学习，用自然语言替代了传统的Q值或策略梯度更新。

## 影响
开创了LLM智能体的在线学习和自我改进方向，启发了Self-Rewarding LM、AutoGPT等自主智能体工作。是LangChain和AI Agent领域的重要里程碑，推动反思/自我批评机制成为智能体设计的标配。

## 相关笔记
- [[ReAct-Synthesizing-Planning-and-Acting]]
- [[Chain-of-Thought-Prompting]]
- [[GPT-4]]
