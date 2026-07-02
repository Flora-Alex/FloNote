---
type: knowledge
tags: [设计模式, 架构, 方法论, 项目总结, 跨项目]
article_id: OBA-2026060306
created_at: 2026/06/03
updated_at: 2026/06/03
---

# 跨项目设计模式与方法论总结

> 从 Flora 生态 5 个项目（[[aiTarot|AITarot]]、[[floraPic|FloraPic]]、[[floraAdmin|Flora Admin]]、[[floraAiAgent|Flora AI Agent]]、[[dotfiles|Dotfiles]]）中提炼的通用设计模式、架构方法论和工程实践。

## 一、设计模式速查表

| 模式 | 项目 | 场景 | 核心思路 |
|---|---|---|---|
| **策略 + 工厂** | Flora Admin | 登录/存储/短信/推送/支付/社交 | Interface + Factory + Spring List 注入 + Map 注册 |
| **模板方法 + 策略混合** | FloraPic | 图片上传 | 骨架算法固定，3 个抽象方法由子类实现 |
| **ReAct Agent 四层继承** | Flora AI Agent | AI 智能体 | BaseAgent → ReActAgent → ToolCallAgent → FloraManus |
| **SSE 流式副作用** | AITarot | AI 流式响应 + 持久化 | Flux.doOnNext 收集 + doOnComplete 保存 |
| **二级缓存** | FloraPic | 图片查询 | Caffeine(L1) + Redis(L2) + 随机 TTL 防雪崩 |
| **AOP 声明式横切** | FloraPic / Flora Admin | 权限/日志/防重提交 | 自定义注解 + 切面拦截 |
| **门面模式** | FloraPic | 以图搜图 | 封装 3 个子 API 的编排 |
| **Disruptor 事件驱动** | FloraPic | WebSocket 消息处理 | RingBuffer 无锁高吞吐 |
| **Advisor 链** | Flora AI Agent | AI 调用前后注入 | 日志 / Re-Reading / RAG |
| **仓储模式** | FloraPic (DDD) | 持久化解耦 | 接口在领域层，实现在基础设施层 |
| **责任链** | Flora Admin | 请求/响应加密 | ResponseBodyAdvice + RequestBodyAdvice |
| **观察者/发布订阅** | Flora Admin | WebSocket 消息分发 | 前端 Map\<string, handler[]\> / 后端 ConcurrentHashMap |

## 二、架构方法论

### 1. 策略工厂统一范式

> 来源：[[floraAdmin|Flora Admin]] — 6 个子系统统一使用

**适用场景**：同一接口、多厂商/多实现需要动态切换。

**实现模板**：
```
1. 定义接口 T
2. 多个 @Component 实现类
3. Factory 类通过 List<T> 注入所有实现
4. @PostConstruct 注册到 Map<String, T>
5. 运行时按配置 key 从 Map 取出
```

**扩展方式**：新增实现只需加 `@Component`，零修改已有代码（[[开闭原则]]）。

### 2. 数据库驱动的动态配置

> 来源：[[floraAdmin|Flora Admin]] — SystemConfigHelper

**核心思路**：所有运行时可变配置存数据库（JSON 格式），通过 Helper 类提供类型安全访问。

**优势**：
- 运维通过管理后台调整，无需改配置文件/重启
- 工厂类实时读取当前配置
- 配置变更即时生效

**vs 配置文件**：适合需要运维频繁调整的场景（存储策略、短信服务商、安全参数等）。静态配置仍用 `application.yml`。

### 3. DDD 四层架构

> 来源：[[floraPic|FloraPic]] — DDD 版本

```
interfaces/     — HTTP 适配（Controller + Assembler + DTO/VO）
application/    — 业务编排（薄代理）
domain/         — 核心业务（实体含方法 + 领域服务 + 仓储接口）
infrastructure/ — 技术实现（仓储实现 + 外部 API + 配置）
```

**关键原则**：
- 领域层对持久化技术无知（仓储接口解耦）
- 实体自身包含业务方法（如 `validPicture()`）
- Assembler 负责层间对象转换
- 应用层是薄代理，不包含业务逻辑

参见 [[DDD]]

### 4. ReAct Agent 框架

> 来源：[[floraAiAgent|Flora AI Agent]] — 四层继承体系

```
BaseAgent（状态机 + 执行循环）
  → ReActAgent（think + act 模板）
    → ToolCallAgent（工具调用实现）
      → 具体智能体（配置工具集 + Prompt）
```

**核心设计决策**：禁用框架内置机制（`withInternalToolExecutionEnabled(false)`），手动管理对话历史和工具调用，获得完全控制权。

**扩展方式**：新增 Agent 只需继承 `ToolCallAgent`，配置工具和 Prompt。

参见 [[agent/agentArchitectures|ReAct]], [[AIAgent]]

### 5. RAG 管道模块化

> 来源：[[floraAiAgent|Flora AI Agent]]

```
文档加载 → 文本切分 → 关键词增强 → 向量存储 → 查询优化 → 检索增强
```

每个组件职责单一、可独立替换。空上下文处理器防止幻觉。

参见 [[RAG]]

### 6. Prompt-as-Contract

> 来源：[[aiTarot|AITarot]]

后端通过 Prompt 约束 AI 按固定格式输出（带 `【标题】` 的段落），前端解析并差异化渲染。前后端对 AI 输出结构有隐式契约。

**扩展**：结构化输出（`.entity(Class)`）是更严格的版本 — AI 输出直接映射为 Java 对象。

参见 [[Prompt]]

## 三、跨切面关注点

### 权限控制

| 项目 | 方案 | 层级 |
|---|---|---|
| Flora Admin | Sa-Token `@SaCheckPermission` + 动态路由 | 前后端双层 |
| Flora Admin | `@DataScope` 数据权限拦截器 | SQL 级 5 级权限 |
| FloraPic | `@AuthCheck` (AOP) + `@SaSpaceCheckPermission` | 系统级 + 空间级 |
| FloraPic | RBAC 权限配置外置 JSON | 角色-权限映射 |

### 统一异常体系

所有 Java 项目统一模式：
```
ErrorCode 枚举 → BusinessException → ThrowUtils.throwIf() → GlobalExceptionHandler → BaseResponse/Result
```

`ThrowUtils.throwIf(condition, errorCode)` — 条件断言式异常抛出。

### SSE 流式响应

| 项目 | 后端实现 | 前端消费 |
|---|---|---|
| AITarot | `Flux<String>` | 原生 fetch + getReader + TextDecoder |
| Flora AI Agent | Flux / ServerSentEvent / SseEmitter（三种） | EventSource API |
| Flora AI Agent | `SseEmitter` + `CompletableFuture.runAsync()` | EventSource + 智能气泡分段 |

### API 契约

| 项目 | 方案 |
|---|---|
| FloraPic | @umijs/openapi 从 OpenAPI 规范自动生成 TS 客户端 |
| Flora Admin | 前端按模块手写 API 层 + request 工具统一封装 |
| AITarot | 前端手写 api/index.js，SSE 与普通 API 分离 |

### 前端状态管理

所有 Vue 3 项目统一使用 [[Pinia]] Composition API 风格（`defineStore` + `setup()`）。

## 四、工程实践清单

| 实践 | 来源 | 说明 |
|---|---|---|
| `.local` 配置分离 | Dotfiles | 主配置可同步，机器差异用 .local 文件 |
| 跨工具键位一致性 | Dotfiles | Vim/IdeaVim 统一 Leader 键和 H/L/J/K |
| 渐进式可选配置 | Dotfiles / Flora AI Agent | 注释即文档，取消注释即启用 |
| 构建产物嵌入 JAR | Flora Admin | Vite 输出到 `resources/static`，单 JAR 部署 |
| 编程式事务 | FloraPic | `TransactionTemplate` 替代 `@Transactional`，更灵活 |
| 异步资源清理 | FloraPic | COS 文件删除用 `@Async`，不阻塞主流程 |
| 演示模式安全兜底 | Flora Admin | `DemoModeInterceptor` 全局禁止写操作 |
| WebSocket 自动重连 | Flora Admin | 前端 WebSocketManager 最多 5 次重连 + 30s 心跳 |
| 禁用框架内置机制 | Flora AI Agent | 当内置机制不满足需求时，主动接管控制权 |

## 五、技术栈交叉引用

| 技术 | 使用项目 |
|---|---|
| Spring Boot | AITarot (3.2.5), FloraPic (2.7.6), Flora Admin (3.2.2), Flora AI Agent (3.4.4) |
| Vue 3 + Composition API | AITarot, FloraPic, Flora Admin, Flora AI Agent |
| Pinia | AITarot, FloraPic, Flora Admin |
| MyBatis-Plus | FloraPic, Flora Admin |
| Sa-Token | FloraPic, Flora Admin |
| Redis | AITarot, FloraPic, Flora Admin |
| Spring AI | AITarot (DeepSeek), Flora AI Agent (DashScope) |
| SSE 流式响应 | AITarot, Flora AI Agent |
| WebSocket | FloraPic, Flora Admin |

## 相关链接

- 项目详情：[[aiTarot]], [[floraPic]], [[floraAdmin]], [[floraAiAgent]], [[dotfiles]]
- 设计模式：[[策略模式]], [[工厂模式]], [[模板方法模式]], [[门面模式]], [[观察者模式]]
- 架构：[[DDD]], [[RBAC]], [[agent/agentArchitectures|ReAct]], [[RAG]]
