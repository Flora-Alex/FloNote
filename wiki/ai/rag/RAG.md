---
type: knowledge
tags: [AI, RAG, 检索增强生成, 知识库]
article_id: OBA-ai-rag
created_at: 2026/05/07
updated_at: 2026/06/24
---

# RAG

RAG（Retrieval-Augmented Generation，检索增强生成）是一种结合信息检索与 [[LLM]] 生成能力的架构。它不把所有知识都固化进模型参数，而是在用户提问时实时从外部知识库检索相关资料，再把检索结果作为上下文交给模型生成答案。

一句话理解：

> RAG 是给大模型开卷考试：模型负责理解和生成，知识库负责提供可更新、可追溯的事实依据。

## 解决的问题

1. **知识时效性**：模型训练完成后知识固定，RAG 可以通过更新知识库接入新内容。
2. **私有知识覆盖**：企业内部文档、产品手册、合同、研报等不会出现在公开训练语料中，RAG 可接入私有知识。
3. **幻觉控制**：RAG 让回答基于检索证据，但只能降低幻觉，不能彻底消除幻觉。
4. **可溯源问答**：答案可以关联到文档、页码、chunk、引用证据。

## RAG vs 微调

微调和 RAG 是互补关系：

| 维度 | 微调 | RAG |
|---|---|---|
| 主要解决 | 怎么说 | 说什么 |
| 知识更新 | 需要重新训练 | 更新知识库即可 |
| 可溯源性 | 弱 | 强，可引用文档/chunk |
| 成本 | 较高 | 较低 |
| 适合 | 语气、格式、领域表达 | 私有知识、动态知识、问答 |

实践中常见组合是：微调负责输出风格和任务格式，RAG 负责提供具体知识。

## 基础流程

一个完整 RAG 系统分为离线和在线两条链路。

### 离线链路

```text
文档接入
→ 文本 / 表格 / 图片解析
→ 清洗与结构化
→ Chunking
→ Embedding
→ 向量库 / 全文索引 / 元数据库入库
```

### 在线链路

```text
用户 Query
→ Query 改写 / 扩展 / 路由
→ Query Embedding
→ 向量检索 / BM25 / 混合召回
→ Rerank 精排
→ Context 构造 / 压缩 / 引用组织
→ LLM 生成
→ 证据核查与反馈记录
```

## Simple RAG 基线

从 `1_基础rag.ipynb` 可以抽象出最小可运行 RAG：

1. 用 PyMuPDF 等工具抽取 PDF 文本。
2. 用固定长度窗口切 chunk，例如 `chunk_size=1000`、`overlap=200`。
3. 为每个 chunk 生成 embedding。
4. 为用户 query 生成 embedding。
5. 用余弦相似度检索 top-k：`cos(q,d)=dot(q,d)/(||q||*||d||)`。
6. 将 top-k chunk 拼成上下文。
7. Prompt 要求模型“只基于上下文回答，资料不足则拒答”。
8. 用参考答案、人工评审或 LLM-as-judge 做初步评估。

这个 baseline 适合教学和 PoC，但不是生产系统：它通常没有向量数据库、metadata 过滤、引用、缓存、错误重试、增量更新和系统化评估。

## 从零 RAG 大师课程脉络

`raw/大模型学习/第4课-RAG (Part 1)：检索增强生成 - 原理与核心组件/从零-RAG大师` 的 0-21 个 notebook 可以压缩成 6 条主线：

| 主线 | Notebook | 合并到 |
|---|---|---|
| 基础 RAG | 0 前言、1 基础 RAG | 本页 |
| 分块与索引增强 | 2 语义分块、3 切块大小、4 上下文增强、5 标题提取、6 文档增强、14 命题分块 | [[ragChunking]] |
| 查询与检索优化 | 7 查询转换、8 重排序、9 相关段落提取、10 上下文压缩、16 融合检索、19 HyDE | [[ragRetrievalOptimization]] |
| 高级 RAG 范式 | 11 反馈闭环、12 自适应检索、13 Self-RAG、15 多模态、17 图 RAG、18 分层检索、20 CRAG、21 RL RAG | [[ragAdvancedPatterns]] |
| 工业级工程 | 第 5 课项目实战、服务化、首字响应、记忆模块 | [[ragIndustrialPractice]] |
| 评估与选型 | 测试集、指标、向量数据库、LlamaIndex | [[ragEvaluation]]、[[ragVectorDatabases]]、[[ragLlamaIndex]] |

## 合并后的 RAG 知识页

- [[ragChunking]] — 文档解析、固定分块、语义分块、上下文标题、问题增强、命题分块。
- [[ragRetrievalOptimization]] — 查询转换、HyDE、混合检索、Rerank、相关段落提取、上下文压缩。
- [[ragAdvancedPatterns]] — Feedback Loop、Adaptive Retrieval、Self-RAG、Multi-modal RAG、Graph RAG、Hierarchical RAG、CRAG、RL RAG。
- [[ragIndustrialPractice]] — 工业级 RAG 项目实战：离线解析、在线召回、服务化、首字响应、记忆模块。
- [[ragEvaluation]] — 检索、生成、证据、性能、体验与测试集构建。
- [[ragVectorDatabases]] — 向量数据库选型：Chroma、Qdrant、Milvus、Weaviate、Pinecone、pgvector。
- [[ragLlamaIndex]] — LlamaIndex 的 Reader、Node Parser、Index、Retriever、Query Engine。

## 实现陷阱

从课程 notebook 中整理出的共性风险：

1. **安全风险**：多个 notebook 输出中出现 API Key 明文。公开或同步前应清理 outputs 并轮换密钥。
2. **教学原型限制**：内存向量库 + NumPy 线性扫描不适合大规模生产。
3. **缺少引用链路**：没有页码、chunk_id、metadata、citation accuracy，难以做证据核查。
4. **语言标签不一致**：部分自适应检索/Self-RAG 代码让 LLM 输出中文标签，但程序判断英文标签，导致策略失效。
5. **LLM-as-judge 不稳定**：适合辅助评估，不应替代人工抽检和可重复指标。
6. **Query 增强可能漂移**：改写、HyDE、子查询分解都可能引入原问题没有的假设。
7. **压缩可能丢证据**：上下文摘要会降低 token 成本，但也可能损失关键细节。

## 原始资料

- `raw/大模型学习/第4课-RAG (Part 1)：检索增强生成 - 原理与核心组件/从零-RAG大师/Readme.md`
- `raw/大模型学习/第4课-RAG (Part 1)：检索增强生成 - 原理与核心组件/从零-RAG大师/0_前言.ipynb` 至 `21_强化学习增强rag.ipynb`
- `raw/大模型学习/第5课-RAG (Part 2)：RAG工业级项目实战/`

## 关联连接

- [[LLM]] — RAG 依赖的大语言模型
- [[AIHallucination]] — RAG 要缓解的幻觉问题
- [[Prompt]] — RAG 中的 prompt 约束和上下文构造
- [[agent/agentToolUse|ToolCalling]] — RAG 与工具调用 / Agentic RAG 的连接
- [[ragChunking]] — 分块与文档处理
- [[ragRetrievalOptimization]] — 检索优化
- [[ragAdvancedPatterns]] — 高级范式
- [[ragEvaluation]] — 评估体系
