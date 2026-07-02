---
type: knowledge
tags: [AI, Agent, 智能体, ToolCalling, Workflow]
article_id: OBA-ai-agent
created_at: 2026/05/07
updated_at: 2026/06/24
---

# AI Agent

[[AIAgent]] 是以 [[LLM]] 为核心“大脑”的自主决策系统。它不是一次性问答，而是围绕目标进行多轮循环：理解任务、规划步骤、调用工具、观察结果、更新记忆、反思纠错，直到任务完成或触发终止条件。

## Agent 与普通 LLM 的区别

| 维度 | 普通 LLM | Agent |
|---|---|---|
| 交互模式 | 单次输入输出 | 多步循环决策 |
| 外部能力 | 依赖模型内部知识 | 可调用工具 / API / 知识库 |
| 状态维护 | 通常无状态 | 维护上下文、记忆、任务状态 |
| 任务处理 | 直接生成答案 | 可规划、执行、观察、重规划 |
| 自主性 | 低 | 高，可决定下一步行动 |

## 核心架构

一个完整 Agent 通常包含：

```text
LLM 大脑
+ Planning 规划
+ Memory 记忆
+ Tools 工具
+ Action / Execution 执行
+ Reflection 反思
+ State / Loop 状态循环
```

核心循环：

```text
Goal
→ Think / Plan
→ Act / Tool Call
→ Observe
→ Update Memory
→ Replan or Finish
```

## Tool、Agent、Workflow 的边界

| 层级 | 决策者 | 特点 | 适合场景 |
|---|---|---|---|
| Tool | 无决策，只执行 | 单一能力函数 | 搜索、计算、发邮件、查库 |
| Agent | LLM 动态决策 | 灵活但不确定 | 路径未知的复杂任务 |
| Workflow | 开发者写死流程 | 可控、可调试 | 固定业务流程 |

实际工程中最常见的是 **Agentic Workflow**：外层 Workflow 控制主流程，在需要灵活判断的节点嵌入 Agent。

## 合并后的 Agent 知识页

- [[agentArchitectures]] — CoT、ReAct、Workflow、Plan-and-Execute、Reflection、Multi-Agent、A2A。
- [[agentToolUse]] — Tool Calling、Function Calling、MCP、Skill、工具优化与微调。
- [[agentMemory]] — 短期记忆、长期记忆、实体记忆、变量记忆、记忆压缩。
- [[agentIndustrialPractice]] — 工业级 Agent 项目、FloraManus、Spring AI、SSE、LLM Gateway。
- [[agentResumeAndEvaluation]] — Agent 评估指标、简历表达、面试追问。

## 落地原则

1. 能用 Workflow 解决的，不要直接上全自主 Agent。
2. 主干流程硬编码，高不确定节点交给 LLM。
3. 工具必须职责单一、schema 明确、错误可读。
4. 必须设置最大步数、超时、预算和终止工具。
5. 所有工具调用都要记录日志，用 badcase 反向优化 prompt、schema 和数据。
6. 高风险操作必须加权限、校验、合规和人工确认。
7. Agent 的能力上限来自“模型 + 工具 + 记忆 + 流程控制 + 评估闭环”的组合，而不是单纯换更强模型。

## 原始资料

- `raw/大模型学习/第6课-Agent 智能体：让 LLM 自主行动/`
- 旧版页面：`wiki/ai/agent/ReAct.md`、`Workflow.md`、`CoT.md`、`ToolCalling.md`、`AgentSkill.md`、`A2A.md`、`LLMGateway.md`

## 关联连接

- [[LLM]] — Agent 的大模型基础
- [[agentArchitectures]] — Agent 架构范式
- [[agentToolUse]] — 工具调用与 Function Calling
- [[agentMemory]] — Agent 记忆系统
- [[agentIndustrialPractice]] — 工程落地
- [[agentResumeAndEvaluation]] — 评估与简历表达
- [[rag/RAG]] — Agent 常用检索工具
