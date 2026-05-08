# 变更日志

## [2026-05-07] ingest | 引入 AI 智能体项目知识库
- **变更**: 新增 [[RAG]], [[AIAgent]], [[ToolCalling]], [[AIHallucination]]; 更新 [[index.md]]
- **冲突**: 无

## [2026-05-07] lint | 为 wiki/ai/ 页面添加 YAML Frontmatter
- **变更**: 为 [[AIHallucination]], [[CoT]], [[MCP]], [[Prompt]], [[RAG]], [[ReAct]], [[ToolCalling]] 添加 YAML 头部; 更新 [[index.md]]
- **冲突**: 无

## [2026-05-07] ingest | 引入 Java 后端项目初始化知识
- **变更**: 新增 [[java项目初始化]]（基于 raw/personal_graph_proj/项目初始化.md 总结）; 为 [[MyBatis Plus]], [[Hutool]], [[Knife4j]] 添加 YAML 头部; 更新 [[index.md]]
- **冲突**: 无

## [2026-05-08] ingest | 根据小林 RAG 面试题更新 RAG 知识库
- **变更**:
  - 大幅扩充 [[RAG]] 文档，新增：RAG vs 微调对比、详细 Chunking 策略、Embedding 模型选型、向量数据库选型、多路召回、Query 改写、Rerank 精排、高级 RAG 范式（Self-RAG/CRAG/GraphRAG/Agentic RAG）、幻觉规避策略、效果评估指标、知识库动态更新、落地难点等内容
  - 新增向量数据库文档：[[Chroma]]、[[Qdrant]]、[[Milvus]]、[[Weaviate]]
  - 更新 [[AIHallucination]]、[[ReAct]]、[[Prompt]]，增加与 RAG 的双向链接
  - 更新 [[index.md]]，添加向量数据库分类索引
- **冲突**: 无

## [2026-05-08] ingest | 根据小林 Agent 面试题更新 Agent 知识库
- **变更**:
  - 大幅扩充 [[AIAgent]] 文档，新增：Agent 与 LLM 本质区别、Tools/Agent/Workflow 三层结构对比、ReAct/Plan-and-Execute/Reflection 三种设计范式、CoT/ToT/GoT 推理模式、四层记忆系统（感知/短期/长期/实体）、记忆压缩方法、Multi-Agent 架构（中心化/去中心化）、Agent 评估指标、框架 vs 手搓对比、落地难点等内容
  - 新增 [[Workflow]] 文档，涵盖 Workflow 编排模式、Agentic Workflow 概念、与 Agent 的对比和选型建议
  - 大幅扩充 [[ReAct]] 文档，新增核心循环详解、实现原理、代码示例、与 Plan-and-Execute 的对比
  - 扩充 [[ToolCalling]] 文档，新增工具设计原则、MCP 协议、在 Agent 中的角色
  - 扩充 [[CoT]] 文档，新增 Zero-shot/Few-shot CoT、与 ReAct/ToT/GoT 的对比、在 Agent 中的应用
  - 更新 [[index.md]]，添加 Workflow 索引，调整 AI 分类结构
- **冲突**: 无

## [2026-05-08] ingest | 根据小林 Function Calling 面试题更新工具调用知识库
- **变更**:
  - 大幅扩充 [[ToolCalling]] 文档，新增：Function Calling 详细原理（三角色分工、schema字段、两轮对话流程、并行调用）、训练原理（SFT教会怎么调、RLHF教会什么时候调、训练数据覆盖场景、RLAIF）、Function Calling vs MCP 对比、Function Calling/Skill/MCP 三层架构、推理模型不支持MCP的原因
  - 大幅扩充 [[MCP]] 文档，新增：MCP解决的核心问题、三层组成结构（角色层/能力层/协议层）、通信方式详解（stdio/Streamable HTTP）、MCP vs Function Calling、MCP vs Agent Skill、MCP vs A2A、实际接入示例、推理模型不支持MCP的原因
  - 新增 [[AgentSkill]] 文档，涵盖 Skill 结构、渐进式加载三层机制、与 Tool/MCP/Prompt/Slash Command 的对比
  - 新增 [[A2A]] 文档，涵盖 Agent Card、Task 生命周期、异步长任务支持、与 MCP 的互补关系
  - 新增 [[LLMGateway]] 文档，涵盖多模型统一接口、API Key管理、限流配额、语义缓存、常见框架对比
  - 更新 [[AIAgent]]，添加 Agent 工具调用架构章节，建立与 MCP/AgentSkill/A2A/ToolCalling 的双向链接
  - 更新 [[index.md]]，添加 AgentSkill、A2A、LLMGateway 索引
- **冲突**: 无

## [2026-05-08] ingest | 根据小林 Java 面试题重构 Backend 知识库
- **变更**:
  - 新增 [[Java基础]] 文档，涵盖面向对象、String、异常、泛型、反射、Lambda
  - 新增 [[Java集合]] 文档，涵盖 List/Map/Set、HashMap 原理、ConcurrentHashMap 演进
  - 新增 [[Java并发]] 文档，涵盖 JMM、锁、线程池、AQS、CompletableFuture、虚拟线程
  - 新增 [[JVM]] 文档，涵盖内存结构、GC、类加载、调优
  - 新增 [[Spring]] 文档，涵盖 IoC、AOP、事务、Spring Boot、Spring MVC
  - 将 [[MyBatis Plus]] 改名为 [[MyBatis]] 并扩充，整合 MyBatis 基础和 MyBatis Plus 增强
  - 创建 [[backend/index|Backend 知识库]] 索引页，重新组织层次结构
  - 更新 [[index.md]]，重构后端开发分类，按核心基础/主流框架/开发工具/项目实战分层
  - 删除 [[MyBatis Plus]] 独立文档（已整合进 MyBatis）
- **冲突**: 无
