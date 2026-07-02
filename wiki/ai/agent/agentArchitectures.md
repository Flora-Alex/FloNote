---
type: knowledge
tags: [AI, Agent, ReAct, Workflow, MultiAgent, A2A]
article_id: OBA-agent-architectures
created_at: 2026/06/23
updated_at: 2026/06/24
---

# Agent 架构范式

[[agentArchitectures]] 汇总 Agent 的主要架构范式：CoT、ReAct、Plan-and-Execute、Workflow、Agentic Workflow、Reflection、Multi-Agent 与 A2A。核心判断标准是：下一步行动由谁决定，流程是否可控，任务是否需要动态探索。

## CoT：思维链

CoT（Chain of Thought）让模型显式分步骤推理。

常见触发方式：

- Zero-shot CoT：例如“让我们一步一步思考”。
- Few-shot CoT：给模型几个带推理过程的样例。

价值：提升复杂推理、数学、逻辑、多步骤问题的表现。

局限：CoT 只能“想”，不能直接与外部世界交互；一旦中间推理错误，后续容易沿错误路径展开。

## ReAct

ReAct = Reasoning + Acting。

核心循环：

```text
Thought → Action → Observation → Thought → ... → Final Answer
```

重要理解：ReAct 由应用程序驱动循环，模型只输出下一步推理和工具调用意图，真实工具执行由系统完成。

优点：

- 实现简单。
- 每一步都可根据工具结果动态调整。
- Thought / Action / Observation 过程透明。

缺点：

- 长任务容易漂移。
- 缺少全局计划。
- 中间错误会传播。
- 工具调用次数和 token 成本可能升高。

适合短链路、探索型、工具结果会影响下一步的问题。

## Plan-and-Execute

把规划和执行拆开：

```text
Planner → Plan → Executor → Result → Replanner
```

优点：全局目标清晰，长流程不容易跑偏；可以用强模型规划、弱模型执行来省成本。

适合多步骤、有依赖、需要全局统筹的任务。

## Workflow

Workflow 是开发者写死的确定性流程。

特点：

- 可控。
- 易调试。
- 成本可预测。
- 灵活性低。

常见模式：

- Prompt Chaining。
- Routing。
- Parallelization。
- Orchestrator-Workers。
- Evaluator-Optimizer。

## Agentic Workflow

生产中最实用的模式：

```text
Workflow 固定主流程
+ Agent 处理灵活节点
+ Tool 执行确定动作
```

原则：能用 Workflow 解决的，不要上全自主 Agent；只有当某个节点需要动态判断时，才升级为 Agent。

## Reflection / Reflexion

Reflection 是质量增强机制：

```text
生成 → 评估 → 改进 → PASS 或达到最大轮次
```

常用于代码生成、文案生成、合规审查、工具调用失败后的重试。

Reflexion 更进一步：把失败原因写入长期记忆，下次避免重复犯错。

## Multi-Agent

Multi-Agent 是多个 Agent 分工协作。

使用动机：

- 单 Agent context 不够。
- 需要专业分工。
- 子任务可并行。
- 需要评审 / 辩论提升质量。

常见模式：顺序流水线、并行扇出、辩论评审、Orchestrator-Workers、去中心化 P2P。

工程上最推荐中心化 Orchestrator 模式，因为可控、可追踪、可调试。

## A2A：Agent-to-Agent

A2A 是 Agent 之间通信协作的协议思路。

核心概念：

- **Agent Card**：描述 Agent 能力、输入输出、认证和端点。
- **Task**：Agent 间协作的任务对象。
- **异步长任务**：任务可持续执行并返回状态。
- **Push Notification**：任务状态变化后通知调用方。

### A2A vs MCP

- MCP：纵向连接 Agent 与工具 / 数据源。
- A2A：横向连接 Agent 与 Agent。

## 架构选型建议

| 场景 | 推荐 |
|---|---|
| 固定业务流程 | Workflow |
| 短链路动态探索 | ReAct |
| 长链路复杂任务 | Plan-and-Execute |
| 质量要求高 | Reflection / Evaluator-Optimizer |
| 子任务专业分工或并行 | Multi-Agent |
| 生产系统 | Agentic Workflow |

## 原始资料

- `raw/大模型学习/第6课-Agent 智能体：让 LLM 自主行动/第七周：Agent 智能体：让 LLM 自主行动.pdf`
- `raw/大模型学习/第6课-Agent 智能体：让 LLM 自主行动/【Agent实战-第1天】规划模块.pdf`
- 旧版页面：`ReAct.md`、`Workflow.md`、`CoT.md`、`A2A.md`、`agentPlanning.md`

## 关联连接

- [[AIAgent]] — Agent 总览
- [[agentToolUse]] — 工具调用
- [[agentMemory]] — 记忆系统
- [[agentIndustrialPractice]] — 工程落地
- [[agentResumeAndEvaluation]] — 评估与表达
