---
type: knowledge
tags: [AI, RAG, 向量数据库, Chroma, Qdrant, Milvus, Weaviate]
article_id: OBA-rag-vector-databases
created_at: 2026/06/23
updated_at: 2026/06/24
---

# RAG 向量数据库选型

[[ragVectorDatabases]] 汇总 [[RAG]] 系统中的向量数据库选型，并合并 Chroma、Qdrant、Milvus、Weaviate 四个旧页面内容。向量数据库负责存储 embedding 向量，并提供高效近似最近邻搜索（ANN）。

## 为什么需要向量数据库

普通关系型数据库的 B-tree 索引不适合高维向量相似度搜索。RAG 系统需要在百万、千万甚至更大规模的 chunk 向量中快速找到相似内容，因此需要专门的向量索引。

核心能力：

- 高维向量存储。
- ANN 近似最近邻搜索。
- metadata 过滤。
- 批量写入与增量更新。
- 多副本、高可用和水平扩展。
- 与 BM25 / sparse vector 的混合检索。

## 常见索引算法

### HNSW

HNSW 构建多层小世界图，从稀疏高层开始导航，逐层逼近相似向量。

特点：召回率高、查询快、内存占用较高。常见参数：`M`、`ef_construction`、`ef/search_ef`。

### IVF

IVF 先将向量聚类到多个桶中，查询时只搜索最相关的桶。

特点：内存占用较低，适合更大规模；召回率通常低于 HNSW，需要调参。

## 选型总览

| 数据库 | 适合场景 | 优点 | 注意事项 |
|---|---|---|---|
| Chroma | 本地 demo、快速原型、中小规模 | 零配置、上手快、生态集成好 | 超大规模和复杂生产能力相对弱 |
| Qdrant | 中小到大规模生产 | Rust 高性能、API 简洁、部署简单、支持分布式和混合检索 | 超大规模仍需调优 |
| Milvus | 大规模、高并发、分布式生产 | 功能全、扩展强、索引类型多、集群能力成熟 | 运维复杂度高 |
| Weaviate | 向量 + 结构化查询 / 复杂过滤 | 多模式搜索、GraphQL-like 查询、结构化过滤强 | 性能和简洁性场景可优先看 Qdrant |
| Pinecone | 不想自运维的云服务 | 全托管、省运维 | 成本、数据出境、厂商绑定 |
| pgvector | 已有 PostgreSQL 项目 | 组件少、接入简单 | 大规模性能弱于专用向量库 |

## 推荐路径

```text
快速原型 → Chroma
中小规模生产 → Qdrant
超大规模分布式 → Milvus
复杂结构化过滤 → Weaviate
已有 PostgreSQL → pgvector
不想运维 → Pinecone
```

不要只用“百万 / 亿级”作为唯一边界，实际还要看：

- QPS。
- 向量维度。
- recall 目标。
- metadata filter 复杂度。
- 数据更新频率。
- 运维能力。
- 云成本和合规要求。

## Chroma

定位：快速原型和本地开发。

特点：

- Python 安装即可用，零配置上手。
- 适合 demo、中小规模项目。
- 和 LangChain / LlamaIndex 集成方便。
- 支持本地、Client-Server 和云托管。
- 新版本支持 BM25 / SPLADE 稀疏向量的混合检索。

适合课程实验、RAG PoC、单机知识库。超大规模场景需要考虑 Qdrant 或 Milvus。

## Qdrant

定位：生产级向量数据库的常见默认选择。

特点：

- Rust 编写，性能好。
- Docker 部署简单。
- API 清晰。
- 默认使用 HNSW。
- 支持分片、副本、分布式。
- 支持混合检索。

适合团队运维资源有限、需要比 Chroma 更稳的生产系统。很多团队一开始用 Chroma 做原型，后来上生产切到 Qdrant。

## Milvus

定位：大规模分布式向量数据库。

特点：

- 分布式架构，读写节点分离。
- 支持 Collection、Segment、Index 等底层管理。
- 支持 HNSW、IVF 等索引。
- 支持 metadata 过滤。
- 可结合 SQ8、mmap 等优化内存。

适合高并发、大规模向量、需要高可用和水平扩展且有足够运维能力的团队。

## Weaviate

定位：向量搜索与结构化过滤结合。

特点：

- 支持向量、关键词、混合搜索。
- 类 GraphQL 查询接口。
- 结构化过滤能力强。
- 模块化 embedding 配置。

适合文档 metadata 很重要、需要复杂过滤条件、向量检索和结构化查询并重的场景。如果主要关注性能和简洁性，Qdrant 可能更合适。

## 性能数据的注意事项

旧页面中保留了一些性能数据参考，例如 P50 / P99 延迟、HNSW 参数、SQ8 内存下降等。这类数据只能作为示例，不能作为通用承诺。

使用时必须说明：硬件配置、向量数量和维度、索引类型和参数、是否包含网络延迟、是否带 metadata filter、recall 目标、并发水平。

## 原始资料

- 旧版 `wiki/ai/rag/Chroma.md`
- 旧版 `wiki/ai/rag/Qdrant.md`
- 旧版 `wiki/ai/rag/Milvus.md`
- 旧版 `wiki/ai/rag/Weaviate.md`
- `第5课-RAG (Part 2)/【RAG实战-第10天】如何提升首字响应速度.pdf`

## 关联连接

- [[RAG]] — RAG 总览
- [[ragRetrievalOptimization]] — 检索优化与混合召回
- [[ragIndustrialPractice]] — 工业级部署与延迟优化
- [[ragEvaluation]] — 检索性能与质量评估
