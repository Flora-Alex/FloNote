---
type: knowledge
tags:
  - AI
  - Agent
  - Skill
  - Claude
article_id: OBA-ai-agentskill
created_at: 2026/05/08
updated_at: 2026/05/08
---

# Agent Skill

## 概述

Agent Skill 是 Anthropic 提出的一种能力打包机制，将**指令、脚本、模板**一体化封装为可复用的能力包。它让 AI Agent 能够像人类"翻阅操作手册"一样，按需获取执行特定任务所需的知识和流程。

## Skill 的目录结构

```
my-skill/
├── SKILL.md          # 核心指令文件（必需）
├── scripts/          # 可执行脚本
├── references/       # 参考文档
└── assets/           # 静态资源（图片、模板等）
```

- **SKILL.md**：Skill 的核心，包含 YAML frontmatter（name、description）和 Markdown 正文（执行指令与步骤）
- **scripts/**：Agent 执行任务时按需调用的脚本
- **references/**：补充性的参考文档，提供背景知识
- **assets/**：模板文件、图片等静态资源

## SKILL.md 格式

```yaml
---
name: skill-name
description: 一句话描述 Skill 的能力
---

# Skill 正文

具体的执行指令、步骤说明、注意事项等 Markdown 内容。
```

## 渐进式加载（Progressive Disclosure）

Skill 的核心设计理念是**三层渐进式加载**，避免一次性将所有内容塞入上下文窗口：

| 层级 | 加载内容 | 触发时机 | Token 开销 |
|------|---------|---------|------------|
| 第一层 | name + description | Agent 初始化时扫描所有 Skill | ~30-50 token/Skill |
| 第二层 | SKILL.md 正文 | Agent 判断需要该 Skill 时加载完整指令 | 数百 token |
| 第三层 | scripts/、references/、assets/ | 执行过程中按需读取具体资源 | 按需 |

这种机制确保 Agent 在拥有大量 Skill 时，不会因为上下文窗口溢出而失效。

## Skill 与相关概念的区别

### Skill vs Tool / [[MCP]]

- **Tool/MCP**：提供**能力**（如搜索、计算、数据库访问），是 Agent 执行动作的"手"
- **Skill**：提供**知识和流程**（如"如何写一份技术方案"），是 Agent 执行任务的"脑"
- 两者互补：Skill 告诉 Agent 该做什么、怎么做，Tool 提供具体的执行能力

### Skill vs [[Prompt]]

- **Prompt**：一次性的临时指令，用完即弃
- **Skill**：持久化的可复用模块，有完整的目录结构和版本管理

### Skill vs Slash Command

- **Slash Command**：需要用户手动触发，是人驱动的
- **Skill**：可以被 Agent **自动发现和调用**，是 Agent 自主决策的结果

## 发展历程

- **2025 年 10 月**：Anthropic 推出 Agent Skill 机制
- **2025 年 12 月**：作为开放标准发布，供社区广泛采用

## 相关链接

- [[AIAgent]] - AI 智能体
- [[ToolCalling]] - 工具调用
- [[MCP]] - 模型上下文协议
