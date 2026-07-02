---
type: knowledge
tags: [AI, Agent, 工业实践, SpringAI, SSE, LLMGateway]
article_id: OBA-agent-industrial-practice
created_at: 2026/06/23
updated_at: 2026/06/24
---

# Agent 工业级项目实战

[[agentIndustrialPractice]] 汇总 Agent 工业级落地、FloraManus / Spring AI 项目结构、SSE 流式交互、工具注册和 LLM Gateway。

## 项目定位

工业级 Agent 不是只写一个 ReAct prompt，而是完整系统：

```text
前端交互
→ 后端 Agent 控制循环
→ 工具系统
→ RAG / 记忆
→ 模型网关
→ 日志 / 评估 / 监控
```

## 前后端服务运行

常见结构：

- Spring Boot / Spring AI 后端。
- Vue 3 / Vite 前端。
- REST + SSE 通信。
- RAG、MCP、工具体系。

## 后端模块结构

```text
agent/        Agent 基类与具体智能体
tools/        工具定义与执行
rag/          检索增强模块
chatmemory/   会话记忆
controller/   API 与 SSE 接口
config/       模型、工具、RAG 配置
```

## Agent 类设计

### BaseAgent

负责通用控制能力：状态机、最大步数、同步 / 流式执行、模板方法。

### ReActAgent

定义 `think()` / `act()` 两阶段。

### ToolCallAgent

负责工具调用、上下文维护、终止检测。

### FloraManus

最终具体智能体，组合工具、模型、RAG 和记忆。

## 工具注册

常见工具：

- 文件操作。
- Web Search。
- Web Scraping。
- 资源下载。
- 终端。
- PDF。
- TerminateTool。

TerminateTool 很重要：它让 Agent 能显式结束任务，避免无限循环。

## Controller 与 SSE

SSE 用于长任务执行过程展示：

```text
用户输入
→ 后端启动 Agent
→ 每一步 Thought / Tool Call / Observation 通过 SSE 推送
→ 最终答案完成
```

适合 Agent 这种多步、耗时、不确定的任务。

## 工程经验

1. 必须有状态机。
2. 必须有最大步数。
3. 必须有 terminate 工具。
4. 工具调用必须写日志。
5. 工具错误要返回给模型，而不是吞掉。
6. 核心循环可自研，周边能力可用框架。
7. 高风险工具要加权限和人工确认。
8. Prompt、schema、工具输出要能通过 badcase 持续迭代。

## LLM Gateway

LLM Gateway 是架在应用和模型 API 之间的中间层。

没有网关时的痛点：

- 多模型接口不统一。
- API Key 分散管理。
- 无法统一限流、计费和审计。
- 模型故障时切换困难。
- 成本和延迟不可观测。

核心功能：

1. 多模型统一接口。
2. API Key 集中管理。
3. 负载均衡与故障转移。
4. 限流与配额管理。
5. 成本追踪与可观测性。
6. Prompt 安全与内容过滤。
7. 语义缓存。

### 语义缓存

语义缓存不是精确字符串缓存，而是对 query 做 embedding，命中语义相似请求后复用答案。

注意：语义缓存可能复用过期或不适用答案，高风险场景要谨慎。

### 常见网关

- LiteLLM。
- Bifrost。
- PortKey。
- Kong AI Gateway。
- One API。

## 原始资料

- `raw/大模型学习/第6课-Agent 智能体：让 LLM 自主行动/`
- `wiki/ai/projects/floraAiAgent` 相关项目沉淀
- 旧版页面：`agentIndustrialPractice.md`、`LLMGateway.md`

## 关联连接

- [[AIAgent]] — Agent 总览
- [[agentArchitectures]] — ReAct、Workflow、Agentic Workflow
- [[agentToolUse]] — 工具系统与 MCP
- [[agentMemory]] — 记忆模块
- [[agentResumeAndEvaluation]] — 项目表达与评估
- [[rag/RAG]] — RAG 能力接入
