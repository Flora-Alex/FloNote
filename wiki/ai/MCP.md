---
type: knowledge
tags: [AI, MCP, 协议, Claude, 工具调用, 安全]
article_id: OBA-ai-mcp
created_at: 2026/05/07
last_updated: 2026/05/07
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
### MCP 架构

#### 1、宏观架构

MCP 的核心是 “⁠客户端 - 服务器” 架构，其中 MCP‌ 客户端主机可以连接到多个服务器。客户端​主机是指希望访问 MCP 服务的程序，比‎如 Claude Desktop、IDE‌、AI 工具或部署在服务器上的项目。

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