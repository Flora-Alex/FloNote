---
type: knowledge
tags: [AI, Agent, ToolCalling, FunctionCalling, MCP, Skill]
article_id: OBA-agent-tool-use
created_at: 2026/06/23
updated_at: 2026/06/24
---

# Agent 工具使用

[[agentToolUse]] 汇总 Agent 工具调用、Function Calling、MCP、Skill、工具调用优化与微调。工具是 Agent 突破模型内部知识和纯文本输出边界的关键。

## 工具调用的角色分工

模型不直接执行工具。典型分工：

```text
模型：决定是否调用工具、调用哪个工具、参数是什么
应用：解析 tool call、执行真实函数/API、把结果回填给模型
工具：完成确定性外部动作
```

## 工具调用流程

```text
用户请求
→ 模型判断需要工具
→ 输出 tool call / function call
→ 应用执行工具
→ 工具结果写回对话
→ 模型基于结果继续推理或给最终答案
```

## 工具设计原则

- 职责单一。
- 命名清晰。
- description 精确。
- required、enum、参数格式明确。
- 错误信息可读。
- 高风险工具加权限、确认、审计、幂等、回滚。
- 工具输出应结构化，方便模型继续使用。

## Function Calling

Function Calling 是模型触发外部函数调用的结构化协议。

关键点：

- 工具定义通常包含 name、description、parameters schema。
- `finish_reason = tool_calls` 表示模型希望调用工具。
- `finish_reason = stop` 表示模型直接回答。
- 工具调用通常是“两轮对话 + 中间执行”。
- 并行工具调用只适用于工具之间无依赖的情况。

## Function Calling 的训练原理

预训练通常学不会稳定工具调用，需要后训练：

- **SFT**：教模型“怎么调工具”，包括格式、参数、调用时机。
- **RLHF / RLAIF**：教模型“什么时候调工具”，减少乱调和漏调。

训练数据需要覆盖：单工具、多工具、缺参追问、失败重试、负例、跨轮变量、相似函数区分。

## MCP、Function Calling、Skill 三层关系

```text
Function Calling：模型触发调用的底层协议
MCP：工具标准化暴露、注册、发现和上下文接入
Agent Skill：把流程、知识、脚本、模板封装成可复用能力包
```

### MCP

MCP（Model Context Protocol）像 AI 应用的 USB 接口，让模型客户端以统一方式连接工具、文件、数据库、浏览器等外部能力。

### Agent Skill

Skill 通常包含：

```text
skill-name/
├── SKILL.md
├── scripts/
├── references/
└── assets/
```

核心机制是渐进式加载：先加载 SKILL.md 的高层说明，需要时再读取脚本、参考资料或资产。

Skill vs Tool：Tool 是单个可执行能力；Skill 是一组流程化知识和工具使用方法。

## Function Calling 优化

先定义评估指标：工具选择准确率、参数正确率、缺参追问率、不必要调用率、工具失败恢复率、任务完成率。

常见 badcase：

- 函数太多或命名相似。
- 参数缺失或类型不符。
- 跨轮上下文变量丢失。
- 前置依赖不明确。
- 参数键被翻译。

体系化优化：

1. 动态工具路由：先筛选候选工具，再交给模型。
2. Schema 优化：精确 description、enum、examples、required。
3. CoT + Plan-Execute：先计划再调用。
4. 结果校验层：类型校验、业务校验、失败重试。
5. 记忆与变量注入：保存关键 ID、上一步结果、用户偏好。
6. 日志驱动调优：沉淀 badcase，反向修 prompt、schema 或训练数据。

## Function Call 微调

什么时候需要微调：

- prompt/schema 优化后仍存在稳定 badcase。
- 工具数量多、参数复杂、业务规则强。
- 有足够高质量训练样本和固定评测集。

什么时候不该微调：

- 工具描述还没写清楚。
- 业务流程还在频繁变化。
- badcase 数量少或不可复现。
- 只是少量 prompt 可修复问题。

## 推理模型与工具调用

长思考链和中途工具调用存在冲突：模型可能在长推理中“想象”工具结果。更稳妥的方式是：思考到需要外部信息时暂停，发出工具调用，拿到结果后继续推理。支持 interleaved thinking 的模型更适合复杂工具链。

## 原始资料

- `raw/大模型学习/第6课-Agent 智能体：让 LLM 自主行动/`
- 旧版页面：`ToolCalling.md`、`AgentSkill.md`、`functionCallingOptimization.md`、`functionCallFineTuning.md`

## 关联连接

- [[AIAgent]] — Agent 总览
- [[agentArchitectures]] — ReAct、Workflow 与 Agentic Workflow
- [[agentMemory]] — 变量记忆与长期记忆
- [[agentIndustrialPractice]] — 工程落地
- [[MCP]] — 模型上下文协议
