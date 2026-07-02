---
type: knowledge
tags: [项目, SpringBoot, Vue3, SSE, SpringAI, Redis, 前后端分离]
article_id: OBA-2026060301
created_at: 2026/06/03
updated_at: 2026/06/03
---

# AITarot — AI 塔罗牌占卜系统

> 前后端分离的智能塔罗牌占卜系统。用户输入问题 → 选择牌阵 → 自动抽牌 → AI 解读 → 多轮对话，强调仪式感和深度 AI 交互体验。

## 技术栈

| 层 | 技术 |
|---|---|
| 后端 | Java 17 / Spring Boot 3.2.5 / Spring AI 1.0.0-M5 |
| AI 模型 | DeepSeek v4 Flash（OpenAI 兼容接口） |
| 会话存储 | Spring Data Redis（24h TTL） |
| 流式响应 | Project Reactor Flux（SSE） |
| 前端 | Vue 3 Composition API / Vite 5 / Pinia / Vue Router 4 |
| 通信 | 原生 fetch + SSE（非 axios） |

## 架构设计

### 后端三层架构

Controller → Service → Model，严格分层不跳层。

```
controller/TarotController     — REST API 入口
service/TarotAiService         — AI 调用核心（统一封装 ChatClient）
service/SessionService         — Redis 会话 CRUD
prompt/PromptTemplates         — Prompt 模板集中管理
model/                         — 实体 + DTO
```

### 前端页面流架构

5 个页面按线性流程串联，通过 [[Pinia]] Store 共享状态：

```
Home → QuestionInput → SpreadSelect → DrawResult → Chat
```

每个页面自包含，最终在 Chat 页面一次性提交所有累积数据。

## 核心设计模式

### 1. SSE 流式副作用模式（Flux + doOnComplete）

在不阻断流式返回的前提下，通过 Reactor 操作符附加副作用（保存到 Redis）：

```java
private Flux<String> collectAndSave(Flux<String> response, String sessionId) {
    StringBuilder fullResponse = new StringBuilder();
    return response
        .doOnNext(chunk -> fullResponse.append(chunk))
        .doOnComplete(() -> sessionService.addMessage(sessionId, "assistant", fullResponse.toString()));
}
```

**通用价值**：任何需要"流式返回 + 持久化"的 [[SSE]] 端点都可复用此模式。

### 2. Prompt-as-Contract 模式

后端通过 Prompt 约束 AI 按固定格式输出（带 `【标题】` 的段落），前端解析并差异化渲染：

- 后端：`PromptTemplates.java` 定义结构化输出约定
- 前端：`parseResponseToSections()` 按 `【标题】` 分割，映射不同颜色主题和图标

**通用价值**：前后端对 AI 输出结构有隐式契约，提升展示质量。参见 [[Prompt]]

### 3. 先缓冲后展示的 SSE 用户体验

前端先完整接收 SSE 流，解析为结构化 sections，再通过 `typewriter()` 逐段打字展示（每次 2-3 字符，间隔 25ms）。

比直接流式输出到页面有更好的控制力：可按段落切换颜色、控制展示节奏。

### 4. 多步表单 + 中心化状态

Pinia Composition API Store 集中管理跨页面状态（sessionId、question、selectedSpread、selectedCards、messages），5 个线性页面累积数据，最终一次性提交。

**通用价值**：向导式交互（Wizard）的标准模式。

### 5. 领域知识注入 Prompt

`SpreadPositionMeanings` 通过静态 Map 将牌阵 ID + 位置索引映射为中文含义，构建 Prompt 时注入 AI，使其理解牌在牌阵中的位置语义。

**通用价值**：这是 [[RAG]] 模式的轻量版 — 不做向量检索，直接将结构化领域知识注入 Prompt。

## Spring AI ChatClient 封装

```java
private Flux<String> aiCall(String prompt) {
    return chatClient.prompt()
        .system(PromptTemplates.SYSTEM_ROLE)
        .user(prompt)
        .stream().content()
        .onErrorResume(e -> Flux.just("\n\n(连接AI服务时出现问题，请稍后重试)"));
}
```

将流式调用、系统角色设定、[[错误处理]]统一封装，所有业务方法复用。`onErrorResume` 实现优雅降级。

## API 设计

统一前缀 `/api/tarot`，SSE 端点统一返回 `Flux<String>`（`MediaType.TEXT_PLAIN_VALUE`）。

| 方法 | 路径 | 功能 | 返回 |
|------|------|------|------|
| POST | `/api/tarot/session` | 创建会话 | String (UUID) |
| POST | `/api/tarot/cards` | 提交选牌 | void |
| POST | `/api/tarot/submit-reading` | 完整占卜 | Flux SSE |
| POST | `/api/tarot/chat` | 多轮对话 | Flux SSE |
| POST | `/api/tarot/interpret` | 牌面解读 | Flux SSE |

## 相关链接

- 技术栈关联：[[SpringAI]], [[SSE]], [[Redis]], [[Pinia]]
- 设计模式关联：[[Prompt]], [[RAG]]
- 原始代码：`raw/i/AITarot/`
