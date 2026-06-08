# Speaker Skill — AI 辅助演讲技能

> GitHub 搜索 "speaker skill" 发现的相关资源和工具。

---

## AI272/speaker — Codex/Claude Code 演讲笔记生成

- **GitHub**: [AI272/speaker](https://github.com/AI272/speaker) ⭐ 145
- **定位**: Codex / Claude Code skill，面向学术演示的演讲稿生成
- **核心功能**:
  - 读取真实 `.pptx` 文件
  - 文本提取 + PPTX 结构解析
  - 每页渲染 + OCR + 视觉审查
  - 生成 grounded speaker notes（基于实际幻灯片的演讲稿）
  - 将脚本写入 PowerPoint 备注区
  - 处理 charts、screenshots、SmartArt、axes、legends、tables、图片内文字
- **技术原理**:
  - python-pptx 解析结构
  - LibreOffice 转图片
  - vision model 读取视觉内容
  - 结合文字+视觉产生完整演讲笔记
- **与你项目的关联**: 作为研究者，可让 AI 自动生成论文汇报的演讲稿，节省准备时间

### 安装方式
```bash
# 通过 Claude Code / Codex 加载 skill
# 仓库内 speaker-v7.skill 为核心 skill 文件
git clone https://github.com/AI272/speaker.git
```

---

## deniseyu/tech-talking — 技术演讲资源

- **GitHub**: [deniseyu/tech-talking](https://github.com/deniseyu/tech-talking) ⭐ 51
- **定位**: 技术演讲技能资源库，面向初学者和有经验的演讲者
- **内容**: 演讲准备、结构设计、Q&A 技巧、幻灯片设计等
- **对你价值**: 学术会议演讲准备参考

---

## 相关

- [[Presentation-Skills|Presentation Skills]] — 演示处理技能集
- [[Vibecoding-Overview|Vibecoding 概述]]
