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
  - 大幅扩充 [[RAG]] 文档，新增：RAG vs 微调对比、详细 Chunking 策略、Embedding 模型选型、向量数据库选型、多路召回、Query 改写、Rerank 精排、高级 RAG 范式（Self-RAG/CRAG/GraphRAG/Agentic RAG）、幻觉规避策略、效果评估指标、知识库动态更新、落地难点
  - 新增向量数据库文档：[[Chroma]], [[Qdrant]], [[Milvus]], [[Weaviate]]
  - 更新 [[AIHallucination]], [[ReAct]], [[Prompt]]，增加与 RAG 的双向链接
  - 更新 [[index.md]]，添加向量数据库分类索引
- **冲突**: 无

## [2026-05-08] ingest | 根据小林 Agent 面试题更新 Agent 知识库
- **变更**:
  - 大幅扩充 [[AIAgent]]，新增：Agent 与 LLM 本质区别、Tools/Agent/Workflow 三层结构、ReAct/Plan-and-Execute/Reflection 范式、CoT/ToT/GoT 推理模式、四层记忆系统、Multi-Agent 架构、评估指标
  - 新增 [[Workflow]]，涵盖编排模式、Agentic Workflow、与 Agent 的对比
  - 大幅扩充 [[ReAct]]，新增核心循环、实现原理、代码示例
  - 扩充 [[ToolCalling]]，新增工具设计原则、MCP 协议
  - 扩充 [[CoT]]，新增 Zero-shot/Few-shot CoT、与 ReAct/ToT/GoT 对比
  - 更新 [[index.md]]，添加 Workflow 索引
- **冲突**: 无

## [2026-05-08] ingest | 根据小林 Function Calling 面试题更新工具调用知识库
- **变更**:
  - 大幅扩充 [[ToolCalling]]，新增：Function Calling 原理、训练原理、FC vs MCP 对比、三层架构
  - 大幅扩充 [[MCP]]，新增：核心问题、三层结构、通信方式、MCP vs FC/Skill/A2A
  - 新增 [[AgentSkill]], [[A2A]], [[LLMGateway]]
  - 更新 [[AIAgent]]，建立与 MCP/AgentSkill/A2A/ToolCalling 的双向链接
  - 更新 [[index.md]]
- **冲突**: 无

## [2026-05-08] ingest | 根据小林 Java 面试题重构 Backend 知识库
- **变更**:
  - 新增 [[Java基础]], [[Java集合]], [[Java并发]], [[JVM]], [[Spring]]
  - 将 [[MyBatis Plus]] 整合并改名为 [[MyBatis]]
  - 更新 [[index.md]]，重构后端开发分类
- **冲突**: 无

## [2026-05-15] ingest | 根据小林大模型面试题更新 LLM 知识库
- **变更**:
  - 新增 [[LLM]]，涵盖大语言模型定义、涌现能力、幻觉机制与缓解、模型选型、评测指标
  - 新增 [[Transformer]]，涵盖 Self-Attention、三种架构变体、MQA/GQA/Flash Attention、RoPE、BPE
  - 新增 [[MoE]]，涵盖混合专家模型、DeepSeek V3/Mixtral/Qwen MoE 对比
  - 新增 [[LLMTraining]]，涵盖三阶段训练、RLHF/DPO/GRPO、LoRA/QLoRA、Scaling Law
  - 新增 [[LLMInference]]，涵盖解码策略、KV Cache、量化、部署框架
  - 更新 [[index.md]]，重构 AI 分类
  - 建立完整的双向链接网络
- **冲突**: 无

## [2026-05-16] ingest | 构建投资与金融知识库
- **变更**:
  - 新增 [[金融市场概览]], [[股票投资入门]], [[期货交易基础]], [[技术分析]], [[K线图]], [[基本面分析]], [[投资组合理论]]
  - 更新 [[index.md]]，新增"时事与投资"分类
- **冲突**: 无
- **备注**: OpenCLI 因 SSL 证书问题未可用，改用 Wikipedia/WebFetch 搜集资料

## [2026-05-16] sync | 移动投资知识库至 current-affairs 目录
- **变更**: 将 wiki/investment/ 整体移至 wiki/current-affairs/investment/; 更新 [[index.md]] 链接路径
- **冲突**: 无
