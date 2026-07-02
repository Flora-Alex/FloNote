---
type: knowledge
tags: [项目, SpringAI, Agent, ReAct, RAG, MCP, SSE, Vue3, 智能体]
article_id: OBA-2026060304
created_at: 2026/06/03
updated_at: 2026/06/03
---

# Flora AI Agent — AI 智能体应用平台

> 基于 [[SpringAI]] 构建的全栈 AI 智能体平台。包含两个核心应用（AI 恋爱大师 + AI 超级智能体）和一个 MCP 图片搜索服务。展示了 [[agent/agentArchitectures|ReAct]] Agent 框架、[[RAG]] 管道、[[MCP]] 协议集成等前沿 AI 工程实践。

## 技术栈

| 层 | 技术 |
|---|---|
| 后端 | Java 21 / Spring Boot 3.4.4 / Spring AI 1.0.0 |
| AI 模型 | 阿里云 DashScope（通义千问 qwen-plus） |
| 向量存储 | SimpleVectorStore（内存）/ PgVector（可选）/ 阿里云知识库（可选） |
| MCP | spring-ai-starter-mcp-client / server-webmvc |
| 序列化 | Kryo 5.6.2（聊天记忆持久化） |
| 前端 | Vue 3 / Vite 4.x / Vue Router 4 |
| 通信 | SSE (Server-Sent Events) / MCP |

## 双应用架构

### LoveApp（ChatClient 模式）

基于 Spring AI `ChatClient` 的声明式 API，通过 [[Advisor]] 链组合功能：

```
记忆 → 日志 → RAG → 工具
```

支持同步/流式/结构化输出多种调用方式。使用 `MessageWindowChatMemory`（20 条消息窗口）管理上下文。

### FloraManus（Agent 模式）

基于自建 Agent 框架的命令式控制，自主维护对话历史，通过 `ToolCallingManager` 手动执行工具调用。

## Agent 框架 — ReAct 四层继承体系

这是项目最核心的架构设计，采用经典的 [[agent/agentArchitectures|ReAct]] 模式：

### 第一层：BaseAgent（抽象基类）

- **状态机**：`IDLE → RUNNING → FINISHED | ERROR`
- **执行循环**：`run()` 方法 for 循环执行 `step()`，支持最大步数限制
- **流式支持**：`runStream()` 使用 `SseEmitter` + `CompletableFuture.runAsync()`
- **关键属性**：`messageList`（自主维护的对话上下文）、`chatClient`、`systemPrompt`、`nextStepPrompt`

### 第二层：ReActAgent（ReAct 模式抽象）

- 定义 `think()` + `act()` 模板方法
- `step()` 方法先调用 `think()` 判断是否需要行动，再调用 `act()` 执行

### 第三层：ToolCallAgent（工具调用实现）

- **关键决策**：禁用 Spring AI 内置工具执行（`withInternalToolExecutionEnabled(false)`），手动管理对话历史
- `think()`：调用 LLM 获取工具调用决策
- `act()`：通过 `ToolCallingManager.executeToolCalls()` 执行工具
- **终止检测**：检查是否调用了 `doTerminate` 工具

### 第四层：FloraManus（具体智能体）

- 配置工具集、System Prompt、最大步数(20)
- 作为 `@Component` 注入，每次请求创建新实例

**设计价值**：新增 Agent 只需继承 `ToolCallAgent` 并配置工具和 Prompt，框架可复用。

## 工具系统

### 集中注册模式

`ToolRegistration` 类通过 `ToolCallback[]` Bean 集中注册所有工具，通过依赖注入在多处复用。

### 工具清单

| 工具 | 功能 |
|---|---|
| `WebSearchTool` | 百度搜索 (SearchAPI.io) |
| `WebScrapingTool` | 网页抓取 (Jsoup) |
| `FileOperationTool` | 文件读写 |
| `ResourceDownloadTool` | 资源下载 |
| `PDFGenerationTool` | PDF 生成 (iText 9) |
| `TerminalOperationTool` | 终端命令执行 |
| `TerminateTool` | 终止工具（让 Agent 主动结束循环） |

### MCP 工具集成

后端通过 `spring-ai-starter-mcp-client` 连接 MCP Server，使用 `ToolCallbackProvider` 获取远程工具。MCP Server 同时支持 SSE（Web 服务）和 stdio（进程通信）两种模式。

参见 [[MCP]]

## RAG 管道

完整的 [[RAG]] 实现，组件职责单一、可独立替换：

```
文档加载 → 文本切分 → 关键词增强 → 向量存储 → 查询优化 → 检索增强
```

| 组件 | 实现 |
|---|---|
| 文档加载 | `LoveAppDocumentLoader`（Markdown + 元数据提取） |
| 文本切分 | `MyTokenTextSplitter`（基于 Token） |
| 关键词增强 | `MyKeywordEnricher`（LLM 自动生成关键词元信息） |
| 向量存储 | SimpleVectorStore / PgVector / 阿里云知识库 |
| 查询优化 | `QueryRewriter`（`RewriteQueryTransformer` 改写查询） |
| 检索增强 | `QuestionAnswerAdvisor` + 自定义 Advisor 工厂 |
| 空上下文处理 | `LoveAppContextualQueryAugmenterFactory`（防止幻觉） |

## Advisor 链模式

[[SpringAI]] 的 Advisor 是拦截器/中间件模式，在 AI 调用前后注入逻辑：

| Advisor | 功能 |
|---|---|
| `MyLoggerAdvisor` | 请求前后日志（CallAdvisor + StreamAdvisor） |
| `ReReadingAdvisor` | Re-Reading 推理增强（追加 "Read the question again"） |

## SSE 流式通信的三种实现

项目展示了 Spring Boot 中 [[SSE]] 的三种实现方式：

| 方式 | 特点 |
|---|---|
| `Flux<String>` | 最简洁 |
| `Flux<ServerSentEvent<String>>` | 可自定义 SSE 事件字段 |
| `SseEmitter` | 最灵活，手动控制生命周期 |

## Prompt 工程实践

- **System Prompt 分层**：`systemPrompt`（角色定义）+ `nextStepPrompt`（每步引导）
- **Re-Reading 技巧**：Advisor 追加 "Read the question again" 提升推理
- **查询改写**：`RewriteQueryTransformer` 优化 RAG 检索
- **空上下文兜底**：RAG 无结果时返回预设回复，防止幻觉
- **结构化输出**：`.entity(LoveReport.class)` 将 LLM 输出解析为 Java Record

## 工程方法论

### 禁用框架内置机制以获得控制权

`ToolCallAgent` 禁用 Spring AI 内置工具执行，手动管理对话历史和工具调用。**当框架内置机制无法满足需求时，主动接管控制权。**

### 渐进式功能开关

通过注释/取消注释切换功能（PgVector、Demo Runner、MCP 配置）。适合教学/演示，生产环境应改用 `@ConditionalOnProperty`。

## 相关链接

- AI 架构：[[agent/agentArchitectures|ReAct]], [[RAG]], [[MCP]], [[SpringAI]], [[AIAgent]], [[agent/agentToolUse|ToolCalling]]
- Prompt 技巧：[[Prompt]], [[agent/agentArchitectures|CoT]]
- 原始代码：`raw/i/flora-ai-agent/`
