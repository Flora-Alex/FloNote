---
type: knowledge
tags: [AI, Agent, 评估, 简历, 面试]
article_id: OBA-agent-resume-evaluation
created_at: 2026/06/23
updated_at: 2026/06/24
---

# Agent 评估与简历表达

[[agentResumeAndEvaluation]] 汇总 Agent 的评估指标、项目表达、简历写法和常见面试追问。Agent 项目不能只说“接了大模型和工具”，必须说明控制策略、工具准确率、成本、延迟和 badcase 闭环。

## Agent 评估维度

### 任务完成度

- 任务成功率。
- 多步骤任务完成率。
- 人工接管率。
- 最大步骤触发率。
- 失败原因分类。

### 工具调用准确性

- 工具选择准确率。
- 参数正确率。
- 缺参追问率。
- 不必要调用率。
- 工具失败修正能力。
- 并行工具调用正确性。

### 效率指标

- 平均步数。
- token 消耗。
- 端到端延迟。
- 工具调用耗时。
- 模型调用次数。
- 成本 / 任务。

### 质量指标

- 幻觉率。
- 用户满意度。
- 合规违规率。
- 输出可读性。
- 引用 / 证据准确率。

## 简历应突出什么

### 1. 工具定义能力

说明你如何设计工具 schema、参数校验、错误返回、权限控制。

### 2. 控制策略

说明使用 ReAct、Workflow、Agentic Workflow、Plan-and-Execute，为什么这样选。

### 3. 决策逻辑

说明什么时候调用工具、什么时候追问、什么时候终止、什么时候 fallback。

### 4. 监控反馈

说明如何记录 tool calls、badcase、成本、延迟，并反向优化 prompt、schema、路由或训练数据。

## 简历表达模板

> 负责 AI Agent 智能体平台的工具调用与执行控制模块。基于 Agentic Workflow 设计主流程，在高不确定节点引入 ReAct 式动态决策；设计文件、搜索、RAG、终端等工具 schema，并加入参数校验、错误回传、最大步数、终止工具和 SSE 流式执行日志。通过工具路由、badcase 回放、schema 优化和记忆变量注入，提升工具调用准确率，降低无效调用和任务失败率。

## 面试讲法

四层讲法：

1. 业务问题：为什么需要 Agent，而不是普通聊天机器人。
2. 技术架构：LLM、Workflow、Tools、Memory、RAG、Gateway、SSE。
3. 技术难点：工具选择、参数正确、无限循环、成本延迟、错误恢复。
4. 量化结果：任务成功率、工具准确率、平均步数、P95 延迟、token 成本、满意度。

## 常见追问

### Agent 和 Workflow 有什么区别？

Workflow 是确定性流程，Agent 是模型动态决策。生产中推荐 Agentic Workflow：主流程固定，不确定节点交给 Agent。

### 如何避免 Agent 无限循环？

- 最大步数。
- 超时。
- 成本预算。
- TerminateTool。
- 状态机。
- 重复动作检测。
- 失败次数阈值。

### 如何提升工具调用准确率？

- 优化工具命名和 description。
- 减少候选工具数量，做工具路由。
- 参数 schema 加 required、enum、格式约束。
- 工具调用前做参数校验。
- 缺参时追问。
- 用 badcase 修 prompt/schema 或做微调。

### 什么时候需要微调 Function Calling？

当 prompt/schema/路由/校验都优化后，仍有稳定、可复现、数量足够的 badcase，并且业务流程相对稳定时，才考虑微调。

## 原始资料

- `raw/大模型学习/第6课-Agent 智能体：让 LLM 自主行动/`
- 旧版页面：`agentResumeAndEvaluation.md`、`functionCallingOptimization.md`、`functionCallFineTuning.md`

## 关联连接

- [[AIAgent]] — Agent 总览
- [[agentArchitectures]] — 架构选型
- [[agentToolUse]] — 工具调用与优化
- [[agentMemory]] — 记忆系统
- [[agentIndustrialPractice]] — 工程落地
