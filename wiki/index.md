# Wiki Index

## AI

### 大模型基础
- [[LLM]] — 大语言模型，定义、涌现能力、幻觉、选型与评测
- [[transformer/Transformer]] — Transformer 总览，架构变体、论文贡献、课程脉络与面试要点
- [[transformer/foundations]] — Transformer 基础，Tokenization、Embedding、位置编码、Q/K/V、Attention、Mask
- [[transformer/inferenceOptimization]] — Transformer 推理优化，KV Cache、MQA/GQA/MLA、RoPE、Flash Attention、解码采样
- [[transformer/modernArchitectures]] — 现代 Transformer 架构，Decoder-only、Llama/Qwen 共性、MoE、长上下文、Reasoning
- [[transformer/practice]] — Transformer 实践，PyTorch 手写 Attention/LayerNorm/简化 Transformer 与 Excel 手算
- [[transformer/transformerComputationProcess]] — Transformer 前向计算过程，结合手搓代码和 AI by Hand Excel 拆解张量流动
- [[llama/llamaOverview]] — Llama 3 总览，Decoder-only 架构与从零推理链路
- [[llama/llamaArchitecture]] — Llama 3 架构，RMSNorm、RoPE、GQA、SwiGLU
- [[llama/llamaTokenizer]] — Llama 3 Tokenizer，tiktoken 风格 BPE 与 special tokens
- [[llama/llamaFromScratch]] — Llama 3 从零推理实践，权重加载、逐层前向与 next token 预测
- [[deepseek/deepseekOverview]] — DeepSeek 系列总览，V2/V3/R1、MLA、MoE 与低成本路线
- [[deepseek/deepseekArchitecture]] — DeepSeek 架构，MLA 与 DeepSeekMoE 的成本优化逻辑
- [[deepseek/deepseekR1]] — DeepSeek-R1，GRPO、冷启动、多阶段 RL、拒绝采样与蒸馏
- [[MoE]] — 混合专家模型，DeepSeek V3、Mixtral、专家并行

### 大模型训练与微调
- [[LLMTraining]] — LLM 三阶段训练（预训练/SFT/对齐）、LoRA/QLoRA 微调、Scaling Law
- [[LLMInference]] — 解码策略、KV Cache、Prompt Caching、量化、部署框架（vLLM/SGLang）

### Agent 与工具
- [[agent/AIAgent]] — AI Agent 总览，LLM + Planning + Memory + Tools + Action + Reflection 的自主系统
- [[agent/agentArchitectures]] — Agent 架构范式，CoT、ReAct、Workflow、Plan-and-Execute、Reflection、Multi-Agent、A2A
- [[agent/agentToolUse]] — Agent 工具使用，Tool Calling、Function Calling、MCP、Skill、工具优化与微调
- [[agent/agentMemory]] — Agent 记忆系统，短期记忆、长期记忆、实体记忆、变量记忆与压缩
- [[agent/agentIndustrialPractice]] — Agent 工业级项目实战，FloraManus、Spring AI、SSE、工具注册、LLM Gateway
- [[agent/agentResumeAndEvaluation]] — Agent 评估与简历表达，工具调用准确率、任务成功率、成本、延迟与面试讲法

### RAG 与向量数据库
- [[rag/RAG]] — 检索增强生成总览，包含 Simple RAG、课程脉络与实现陷阱
- [[rag/ragChunking]] — RAG 文档解析与 Chunking，固定分块、语义分块、标题增强、问题增强、命题分块
- [[rag/ragRetrievalOptimization]] — RAG 检索优化，Query Rewrite、HyDE、Hybrid Search、Rerank、相关段落提取、上下文压缩
- [[rag/ragAdvancedPatterns]] — 高级 RAG 范式，Feedback Loop、Self-RAG、CRAG、Graph RAG、分层检索、多模态 RAG、RL RAG
- [[rag/ragIndustrialPractice]] — RAG 工业级项目实战，离线解析、在线召回、服务化、首字响应、记忆模块
- [[rag/ragEvaluation]] — RAG 评估与测试集构建，Precision@K、Recall@K、MRR、Faithfulness、P95/P99、实现陷阱
- [[rag/ragVectorDatabases]] — RAG 向量数据库选型，Chroma、Qdrant、Milvus、Weaviate、Pinecone、pgvector
- [[rag/ragLlamaIndex]] — RAG LlamaIndex 组件，Reader、Node Parser、Index、Retriever、Query Engine

### 其他 AI 主题
- [[AIHallucination]] — 大模型幻觉，生成看似合理但实际不准确的内容
- [[Prompt]] — 提示词工程，设计高质量 Prompt 的方法与技巧

## Claude 生态
- [[MCP]] — 模型上下文协议，AI 应用的 USB 接口

## 后端开发
- [[java项目初始化]] — Spring Boot 项目初始化完整流程
- [[用户模块开发]] — 用户模块开发实战
- [[Java基础]] — 面向对象、String、异常、泛型、反射、Lambda
- [[Java集合]] — List/Map/Set、HashMap 原理、ConcurrentHashMap 演进
- [[Java并发]] — JMM、锁、线程池、AQS、CompletableFuture、虚拟线程
- [[JVM]] — 内存结构、GC、类加载、调优
- [[Spring]] — IoC、AOP、事务、Spring Boot、Spring MVC
- [[MyBatis]] — MyBatis 基础 + MyBatis Plus 增强

## 开发工具
- [[Git]] — 版本控制基础命令
- [[README-devtools]] — 开发工具配置总览（Zsh、Git、Starship、别名）
- [[macSetup]] — Mac 配置还原指南（完整环境搭建）
- [[zshAndShell]] — Zsh 配置指南（Oh-My-Zsh、Starship、别名、函数）
- [[gitConfig]] — Git 配置指南（别名、颜色、行为）
- [[gitignore]] — Gitignore 配置指南（忽略规则、安全）
- [[ghostty]] — Ghostty 终端配置指南（快捷键、主题、优化）

## 项目知识沉淀
- [[projects/designPatternsCollection]] — 跨项目设计模式与方法论总结（策略工厂、DDD、ReAct Agent、SSE 流式、二级缓存等）
- [[projects/floraAdmin]] — Flora Admin 企业级后台（六层 Maven 模块化、策略工厂 ×6、动态配置中心、RSA+AES 加密）
- [[projects/floraPic]] — FloraPic 云图库平台（双架构并行：标准分层 + DDD、Disruptor 事件驱动、动态分表）
- [[projects/floraAiAgent]] — Flora AI Agent 智能体平台（ReAct 四层继承、RAG 管道、MCP 协议、Advisor 链）
- [[projects/aiTarot]] — AITarot AI 塔罗牌（SSE 流式副作用、Prompt-as-Contract、多步表单状态管理）
- [[projects/dotfiles]] — Dotfiles 开发环境配置（分层配置、跨工具键位一致性、渐进式可选配置）

## 效率工具
- [[alfred/README]] — Alfred 使用指南（入口文档）
- [[alfred/gettingStarted]] — 入门指南和基础配置
- [[alfred/basicFeatures]] — 核心功能详解（搜索、剪贴板、片段）
- [[alfred/shortcuts]] — 快捷键大全
- [[alfred/advancedFeatures]] — 高级功能（Universal Actions、文件过滤器）
- [[alfred/workflows]] — 工作流开发指南
- [[alfred/tips]] — 实用技巧和最佳实践
