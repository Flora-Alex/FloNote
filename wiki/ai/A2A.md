---
type: knowledge
tags:
  - AI
  - Agent
  - A2A
  - 协议
  - Multi-Agent
article_id: OBA-ai-a2a
created_at: 2026/05/08
updated_at: 2026/05/08
---

# A2A（Agent-to-Agent）

## 概述

A2A（Agent-to-Agent）是 Google 发布的开放协议，旨在解决多个 AI Agent 之间的**通信与协作**问题。它定义了一套标准化的方式，让不同框架、不同厂商构建的 Agent 能够互相发现、对话和协同完成任务。

## 为什么需要 A2A

单个 Agent 存在天然的天花板：

1. **工具数量限制**：一个 Agent 不可能集成所有工具，工具过多会导致选择困难
2. **上下文窗口限制**：复杂任务的中间过程会快速消耗上下文
3. **专业能力限制**：通用 Agent 难以在所有领域都达到专家水平

多 Agent 协作的核心收益是**上下文压力隔离**：每个 Agent 独立处理自己负责的子任务，在各自的上下文中完成中间推理过程，只将最终结论返回给协调者，大幅降低单个 Agent 的上下文压力。

## 核心概念

### Agent Card

每个 A2A Agent 发布一张 JSON 格式的"名片"，供其他 Agent 发现和了解自己的能力。

- **路径**：`/.well-known/agent-card.json`
- **内容**：Agent 名称、描述、Skill 列表、是否支持流式响应、认证方式等

```json
{
  "name": "research-agent",
  "description": "负责信息检索和分析",
  "skills": ["web-search", "paper-analysis"],
  "supportsStreaming": true
}
```

### Task

Task 是 A2A 协议中任务协作的**基本单位**，拥有完整的生命周期状态管理：

```
submitted → working → completed
                  ↘ failed
```

- **submitted**：任务已提交，等待 Agent 接收
- **working**：Agent 正在处理中
- **completed**：任务成功完成，返回结果
- **failed**：任务执行失败

### 异步长任务与 Push Notification

A2A 原生支持**异步长任务**。对于耗时较长的任务，客户端无需持续等待，Agent 完成后通过 Push Notification 主动通知结果。

## 架构本质

A2A 的架构本质是 **Agent 的微服务化**：

| 微服务架构 | A2A 架构 |
|-----------|---------|
| 服务注册与发现 | Agent Card |
| API 接口定义 | Task 交互协议 |
| 服务间通信 | Agent-to-Agent 消息传递 |
| 负载均衡与容错 | 多 Agent 协作与故障转移 |

## A2A vs [[MCP]]

| 维度 | MCP | A2A |
|------|-----|-----|
| 通信方向 | **纵向**：Agent 向下连接工具 | **横向**：Agent 之间互相连接 |
| 解决问题 | Agent 如何使用外部工具 | Agent 之间如何协作 |
| 协议角色 | 工具提供方 ↔ Agent 使用方 | Agent ↔ Agent（对等） |
| 类比 | USB 接口（连接外设） | 网络协议（设备互联） |

两者是**互补关系**：MCP 让 Agent 获得工具能力，A2A 让多个 Agent 协同完成更复杂的任务。

## 相关链接

- [[AIAgent]] - AI 智能体
- [[MCP]] - 模型上下文协议
- [[ToolCalling]] - 工具调用
