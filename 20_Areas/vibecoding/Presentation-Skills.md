# Presentation Skills — 演示处理技能集

> GitHub: [pamelafox/presentation-skills](https://github.com/pamelafox/presentation-skills) ⭐ 88

AI agent 用的演示处理 skill 集合。可直接用于 Claude Code / Copilot / Codex 等 agent 环境。

## Skills 清单

| Skill | 功能 |
|-------|------|
| **pdf-to-markdown** | PDF / PPTX / DOCX / XLSX 转 Markdown（基于 Microsoft markitdown） |
| **extract-slide-text** | 提取 PPTX 幻灯片文本 |
| **convert-slides-to-images** | PPTX 每页转图片（方便视觉审查） |
| **outline-slides** | 从 PDF/PPTX 生成大纲 |
| **review-presentation** | AI review 演示文稿内容 |
| **generate-writeup** | 根据演示文稿生成文字总结 |
| **fetch-slides** | 从 SpeakerDeck / SlideShare 获取幻灯片 |
| **capture-video-frames** | 从演示视频中提取帧 |
| **extract-transcript** | 提取演讲转录文本 |
| **discussion-commenter** | 生成讨论评论 |
| **youtube-description** | 根据幻灯片生成 YouTube 视频简介 |
| **youtube-live-chat** | 处理直播聊天 |
| **make-revealjs-presentation** | 用 RevealJS 生成 HTML 演示文稿 |

## 安装

```bash
# npx
npx skills pamelafox/presentation-skills

# gh CLI
gh repo clone pamelafox/presentation-skills
```

## 对你项目的价值

- 论文汇报 PPT 自动提取文本 → 辅助生成演讲稿
- 从 PPT 生成论文汇报大纲
- 将论文答辩 PPT 转为文字稿

## 相关

- [[Speaker-Skill|Speaker Skill]] — 演讲技能
- [[Vibecoding-Overview|Vibecoding 概述]]
