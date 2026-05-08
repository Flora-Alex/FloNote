---
type: knowledge
tags:
  - AI
  - RAG
  - 向量数据库
  - Chroma
article_id: OBA-ai-chroma
created_at: 2026/05/08
updated_at: 2026/05/08
---

### Chroma 向量数据库

Chroma 是一个开源的向量数据库，专为 AI 和 [[RAG]] 应用设计，以简单易用著称。

#### 核心特点

**零配置上手**：Python 直接 `pip install` 即可使用，本地内存运行，无需额外配置，配合 LangChain/LlamaIndex 原生集成非常方便。

**Client-Server 模式**：Chroma 现在支持了 Client-Server 模式和 Chroma Cloud 托管服务，不再只是本地嵌入式数据库。

**混合检索支持**：2025 年加入了 BM25/SPLADE 稀疏向量的混合检索支持，可以同时做语义检索和关键词检索。

#### 适用场景

- 快速原型验证和 Demo 开发
- 中小规模的生产使用
- 本地开发和测试环境

#### 注意事项

Chroma 的分布式能力还在成熟中，对于超大规模（千万级以上）的生产场景，它的稳定性和性能还不如 [[Milvus]] 和 [[Qdrant]] 这些专门为大规模设计的方案。

#### 选型建议

| 场景 | 建议 |
| --- | --- |
| 快速原型 | 首选 Chroma |
| 中小规模生产 | 可用 Chroma，但要关注性能 |
| 超大规模 | 考虑切换到 Qdrant 或 Milvus |

Chroma 适合快速原型验证和中小规模的生产使用，超大规模场景需要考虑其他选项。
