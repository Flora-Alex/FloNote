---
type: knowledge
tags: [AI, MCP, 协议, Claude, 工具调用, 安全]
article_id: OBA-ai-mcp
created_at: 2026/05/07
last_updated: 2026/05/08
---
### 什么是 MCP？

MCP（Model Co⁠ntext Protocol，模型上下文协议）是‌一种开放标准，目的是增强 AI 与外部系统的交互​能力。MCP 为 AI 提供了与外部工具、资源和‎服务交互的标准化方式，让 AI 能够访问最新数据‌、执行复杂操作，并与现有系统集成。

根据 [官方定义](https://modelcontextprotocol.io/introduction)，MCP 是一种开放协议，它标准化了应用程序如何向大模型提供上下文的方式。可以将 MCP 想象成 AI 应用的 USB 接口。就像 USB 为设备连接各种外设和配件提供了标准化方式一样，MCP 为 AI 模型连接不同的数据源和工具提供了标准化的方法。
![[Pasted image 20260507134437.png]]
前面说的可能有些抽象，让我举些例子帮大家理解 MCP 的作用。首先是 **增强 AI 的能力**，通过 MCP 协议，AI 应用可以轻松接入别人提供的服务来实现更多功能，比如搜索网页、查询数据库、调用第三方 API、执行计算。

其次，我们一定要记住 MCP 它是个 **协议** 或者 **标准**，它本身并不提供什么服务，只是定义好了一套规范，让服务提供者和服务使用者去遵守。这样的好处显而易见，就像 HTTP 协议一样，现在前端向后端发送请求基本都是用 HTTP 协议，什么 get / post 请求类别、什么 401、404 状态码，这些标准能 **有效降低开发者的理解成本**。

这就是 MCP 的三大作用：

- 轻松增强 AI 的能力
- 统一标准，降低使用和理解成本
- 打造服务生态，造福广大开发者

### MCP 解决的核心问题

要理解 MCP 的价值，先看没有 MCP 之前接工具的痛点：

- **碎片化**：每接一个新工具都要单独写集成代码、处理认证、适配数据格式
- **难复用**：在 Claude Desktop 里接好的工具，换个客户端就得重新接一遍
- **强绑定**：工具集成代码和特定模型、特定客户端深度耦合，换个模型就得重写，换个客户端也得重写

MCP 的核心思路是 **"工具实现一次，到处复用"**：工具提供方按照 MCP 标准实现一个 Server，所有支持 MCP 的客户端都能直接使用，无需重复开发。这和 [[agent/agentToolUse|ToolCalling]] 中提到的 Function Calling 有本质区别——Function Calling 解决的是模型层面的调用格式问题，而 MCP 解决的是工具接入的标准化问题（详见后文 [[#MCP vs Function Calling]] 对比）。

### MCP 架构

#### 1、宏观架构

MCP 的核心是 "⁠客户端 - 服务器" 架构，其中 MCP‌ 客户端主机可以连接到多个服务器。客户端​主机是指希望访问 MCP 服务的程序，比‎如 Claude Desktop、IDE‌、AI 工具或部署在服务器上的项目。

#### 2、SDK 3 层架构

如果我们要在程序中使用 MCP 或开发 MCP 服务，可以引入 MCP 官方的 SDK，比如 [Java SDK](https://modelcontextprotocol.io/sdk/java/mcp-overview)。让我们先通过 MCP 官方文档了解 MCP SDK 的架构，主要分为 3 层：

分别来看每一层的作用：

- 客户端 / 服务器层：McpClient 处理客户端操作，而 McpServer 管理服务器端协议操作。两者都使用 McpSession 进行通信管理。
- 会话层（McpSession）：通过 DefaultMcpSession 实现管理通信模式和状态。
- 传输层（McpTransport）：处理 JSON-RPC 消息序列化和反序列化，支持多种传输实现，比如 Stdio 标准 IO 流传输和 HTTP SSE 远程传输。

#### 3、MCP 客户端

MCP Client 是⁠ MCP 架构中的关键组件，主要负责和 MCP‌ 服务器建立连接并进行通信。它能自动匹配服务器​的协议版本、确认可用功能、负责数据传输和 JS‎ON-RPC 交互。此外，它还能发现和使用各种‌工具、管理资源、和提示词系统进行交互。

除了这些核心功⁠能，MCP 客户端还支持一‌些额外特性，比如根管理、采​样控制，以及同步或异步操作‎。为了适应不同场景，它提供‌了多种数据传输方式，包括：

- Stdio 标准输入 / 输出：适用于本地调用
- 基于 Java HttpClient 和 WebFlux 的 SSE 传输：适用于远程调用

客户端可以⁠通过不同传输方式调‌用不同的 MCP ​服务，可以是本地的‎、也可以是远程的。‌如图：
![[Pasted image 20260507134505.png]]

#### 4、MCP 服务端

MCP S⁠erver 也是整‌个 MCP 架构的​关键组件，主要用来‎为客户端提供各种工‌具、资源和功能支持。

它负责处理客户端⁠的请求，包括解析协议、提供工具‌、管理资源以及处理各种交互​信息。同时，它还能记录日志、发送通‎知，并且支持多个客户端同时连接‌，保证高效的通信和协作。

和客户端一样，它也⁠可以通过多种方式进行数据传输，比如‌ Stdio 标准输入 / 输出、​基于 Servlet / WebF‎lux / WebMVC 的 SS‌E 传输，满足不同应用场景。

这种设计使⁠得客户端和服务端完‌全解耦，任何语言开​发的客户端都可以调‎用 MCP 服务。‌如图：
![[Pasted image 20260507134511.png]]

### MCP 的三层组成结构

从更高维度理解，MCP 由三个层次组成：**角色层**、**能力层**、**协议层**。

#### 角色层

MCP 架构中有三个核心角色：

| 角色 | 说明 | 示例 |
|------|------|------|
| **Host（宿主应用）** | 用户直接使用的 AI 应用 | Claude Desktop、Cursor、IDE |
| **Client（客户端）** | Host 内部负责与 Server 通信的模块 | Claude Desktop 内置的 MCP Client |
| **Server（服务端）** | 工具提供方实现的独立进程 | GitHub MCP Server、数据库 MCP Server |

**Host 和 Client 的区别**：Host 是宿主应用本身（用户看到的产品），Client 是 Host 内部负责和 Server 通信的模块。一个 Host 可以同时连接多个 Server，每个 Server 对应一个 Client 实例。

#### 能力层

MCP Server 可以向 Client 提供三种能力（详见 [[#MCP 核心概念]]）：

| 能力 | 本质 | 特点 |
|------|------|------|
| **Tools（工具）** | 改变世界 | 有副作用的操作，如执行命令、发送邮件、创建文件 |
| **Resources（资源）** | 观察世界 | 只读数据，如文件内容、数据库记录、API 响应 |
| **Prompts（提示词）** | 结构化表达 | 可复用的提示词模板，如代码审查模板、翻译模板 |

三者的本质区别可以用一句话概括：**Tools 改变世界，Resources 观察世界，Prompts 结构化表达**。

#### 协议层

- **消息格式**：JSON-RPC 2.0（请求/响应/通知）
- **传输方式**：stdio（本地）或 Streamable HTTP（远程）
- **设计理念**：消息格式和传输方式解耦，同一份 JSON-RPC 消息可以通过不同传输方式发送

### MCP 通信方式详解

MCP 支持两种主要的传输方式：

#### stdio（标准输入/输出）

- Server 作为本地子进程运行，通过 stdin/stdout 管道与 Client 通信
- **延迟极低**，不需要网络开销
- 适合本地开发场景，如 Claude Desktop 调用本地工具
- 每次启动 Client 时会拉起 Server 进程，退出时终止

#### Streamable HTTP

- Server 作为 HTTP 服务部署，Client 通过 HTTP 请求与之通信
- 支持多个 Client 共享同一个 Server
- 适合生产环境部署和远程访问

> [!note] 传输方式演进
> 早期规范（2024-11-05）采用 **HTTP + SSE 双端点**方案：一个端点处理 Client → Server 的请求（POST），另一个端点处理 Server → Client 的推送（SSE）。该方案已被标记为 **deprecated**。
>
> 新的 **Streamable HTTP** 方案将双端点合并成一个 `/mcp` 端点，并非抛弃 SSE，而是将 SSE 流内嵌在 HTTP 响应中，简化了部署和连接管理。

#### JSON-RPC 2.0 消息格式示例

```json
// 请求（Client → Server）
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "get_weather",
    "arguments": { "city": "Beijing" }
  }
}

// 响应（Server → Client）
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [{ "type": "text", "text": "Beijing: Sunny, 25°C" }]
  }
}

// 通知（Server → Client，无 id 字段）
{
  "jsonrpc": "2.0",
  "method": "notifications/tools/list_changed"
}
```

### MCP 核心概念

按照官方的说法⁠，总共有 6 大核心概念。
1. [Resources 资源](https://modelcontextprotocol.io/docs/concepts/resources#resources)：让服务端向客户端提供各种数据，比如文本、文件、数据库记录、API 响应等，客户端可以决定什么时候使用这些资源。使 AI 能够访问最新信息和外部知识，为模型提供更丰富的上下文。
2. [Prompts 提示词](https://modelcontextprotocol.io/docs/concepts/prompts)：服务端可以定义可复用的提示词模板和工作流，供客户端和用户直接使用。它的作用是标准化常见的 AI 交互模式，比如能作为 UI 元素（如斜杠命令、快捷操作）呈现给用户，从而简化用户与 LLM 的交互过程。
3. [Tools 工具](https://modelcontextprotocol.io/docs/concepts/tools)：MCP 中最实用的特性，服务端可以提供给客户端可调用的函数，使 AI 模型能够执行计算、查询信息或者和外部系统交互，极大扩展了 AI 的能力范围。
4. [Sampling 采样](https://modelcontextprotocol.io/docs/concepts/sampling)：允许服务端通过客户端向大模型发送生成内容的请求（反向请求）。使 MCP 服务能够实现复杂的智能代理行为，同时保持用户对整个过程的控制和数据隐私保护。
5. [Roots 根目录](https://modelcontextprotocol.io/docs/concepts/roots)：MCP 协议的安全机制，定义了服务器可以访问的文件系统位置，限制访问范围，为 MCP 服务提供安全边界，防止恶意文件访问。
6. [Transports 传输](https://modelcontextprotocol.io/docs/concepts/transports)：定义客户端和服务器间的通信方式，包括 Stdio（本地进程间通信）和 SSE（网络实时通信），确保不同环境下的可靠信息交换。

如果要开发⁠ MCP 服务，我‌们主要关注前 3 ​个概念，当然，To‎ols 工具是重中‌之重！

[MCP 官方文档](https://modelcontextprotocol.io/clients) 中提到，大多数客户端也只支持 Tools 工具调用能力：

### MCP vs Function Calling

MCP 和 [[agent/agentToolUse|ToolCalling]] 不是竞争关系，而是不同层面的东西：

| 维度 | Function Calling | MCP |
|------|-----------------|-----|
| **解决的问题** | 模型怎么输出调用请求 | 工具怎么标准化接入 |
| **作用层面** | 模型层（LLM 能力） | 应用层（工具接入标准） |
| **关注点** | 模型输出结构化的工具调用格式 | 工具的发现、注册、通信、安全 |
| **依赖关系** | 是 MCP 的底层驱动机制 | 上层建立在 Function Calling 之上 |

关键理解：

- **MCP 底层靠 Function Calling 驱动**：MCP Client 把 Server 提供的工具描述转成 Function Calling 的 function definitions 传给模型，模型通过 Function Calling 输出调用请求，Client 再通过 MCP 协议转发给 Server 执行
- **模型感知不到 MCP 的存在**：模型只知道有 Function Calling，不知道 MCP 协议的存在。MCP 是应用层面的抽象，对模型透明
- **什么时候选 Function Calling**：只需要接一两个简单工具、一次性使用、不需要复用
- **什么时候选 MCP**：需要接入多个工具、希望跨客户端复用、需要标准化管理

### MCP vs Agent Skill

MCP 和 [[agent/agentToolUse|AgentSkill]] 分别赋予 [[AIAgent]] 不同维度的能力：

| 维度 | MCP | Agent Skill |
|------|-----|-------------|
| **提供什么** | 工具和数据的访问能力 | 完成任务的知识和流程 |
| **比喻** | Agent 的"手" | Agent 的"操作手册" |
| **粒度** | 原子操作（调用一个 API、读一个文件） | 工作流程（多步骤任务编排） |
| **示例** | 搜索网页工具、读取数据库工具 | "写一篇技术博客"的完整流程 |

两者的配合关系：**Skill 编排流程，MCP 提供工具**。一个 Skill 在执行过程中可能需要调用多个 MCP Tool 来完成具体操作。

### MCP vs A2A

MCP 和 A2A（Agent-to-Agent）协议解决不同方向的连接问题：

| 维度 | MCP | A2A |
|------|-----|-----|
| **连接方向** | Agent 向下连工具（纵向） | Agent 向外连其他 Agent（横向） |
| **解决的问题** | 标准化工具接入 | 标准化 Agent 间协作 |
| **类比** | USB 接口（连外设） | 网络协议（连其他计算机） |

两者是 **互补关系，不冲突**：一个 Agent 可以通过 MCP 接入工具来增强自身能力，同时通过 A2A 与其他 Agent 协作完成更复杂的任务。

### MCP 实际接入示例

#### Claude Desktop 配置文件示例

Claude Desktop 的 MCP 配置文件位于：

- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_xxxx"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/dir"]
    }
  }
}
```

每个 Server 条目指定了启动命令、参数和环境变量。Claude Desktop 启动时会拉起这些 Server 子进程，通过 stdio 与之通信。

#### 自己写 MCP Server（Python 示例）

以下是一个最简 MCP Server，不超过 30 行代码：

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("my-tools")

@mcp.tool()
def add(a: int, b: int) -> int:
    """两数相加"""
    return a + b

@mcp.tool()
def get_time() -> str:
    """获取当前时间"""
    from datetime import datetime
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")

if __name__ == "__main__":
    mcp.run(transport="stdio")
```

运行后，任何支持 MCP 的 Client 都能发现并调用 `add` 和 `get_time` 两个工具。

### 推理模型不支持 MCP 的原因

目前推理模型（如 Claude 3.5 Sonnet 的 extended thinking 模式、o1/o3 等）对 MCP 的支持有限制，原因如下：

1. **思考链不能中途打断**：推理模型在思考阶段（thinking）会进行长时间的内部推理，这个过程不能被工具调用中断
2. **MCP 底层依赖 Function Calling**：MCP Client 需要通过 Function Calling 让模型输出工具调用请求，但推理模型在思考阶段无法执行 Function Calling

**解决方案**：工具调用发生在思考阶段结束之后。即模型先完成完整的思考过程，再根据思考结果决定是否调用工具。这意味着推理模型使用 MCP 时，工具调用不会出现在思考过程中，而是在思考完成后作为一个独立的步骤执行。

---

> [!related]
> - [[agent/agentToolUse|ToolCalling]] — MCP 底层依赖 Function Calling 驱动，两者是不同层面的抽象
> - [[AIAgent]] — MCP 为 Agent 提供标准化的工具接入能力
> - [[agent/agentToolUse|AgentSkill]] — Skill 编排流程，MCP 提供工具，两者配合使用
