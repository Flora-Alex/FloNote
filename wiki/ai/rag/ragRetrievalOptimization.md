---
type: knowledge
tags: [AI, RAG, 检索优化, Rerank, HybridSearch, HyDE]
article_id: OBA-rag-retrieval-optimization
created_at: 2026/06/23
updated_at: 2026/06/24
---

# RAG 检索优化

[[ragRetrievalOptimization]] 汇总 RAG 课程中的查询转换、融合检索、重排序、相关段落提取、上下文压缩与 HyDE。Naive RAG 只做“query embedding → vector top-k → prompt”，在真实业务中通常不够。

## 优化总览

```text
Query 理解 / 转换
→ 多路召回
→ 结果融合
→ Rerank 精排
→ 上下文压缩 / 相关段落提取
→ Prompt 构造
```

典型生产组合：

```text
Parent-Child Chunking
+ Dense Vector Search
+ BM25 / Sparse Search
+ RRF 融合
+ Cross-Encoder Rerank
+ Context Compression
```

## Query Transformation

`7_查询转换.ipynb` 覆盖三类查询转换。

### Query Rewrite

把口语化、模糊或关键词不足的问题改写成更明确的检索语句。

```text
这东西坏了咋整？
→ 产品故障排查与维修流程是什么？
```

### Step-back Prompting

从具体问题退一步，生成更上位的问题，用于召回背景知识。

```text
Transformer 为什么要用 RoPE？
→ Transformer 如何编码位置信息？
```

### Sub-query Decomposition

将复杂问题拆成多个子问题分别检索，再合并结果。适合多跳问答、对比分析和跨实体问题。

风险：查询转换可能引入原问题没有的假设；Step-back 可能召回过宽；子查询分解会增加成本。

## HyDE

`19_假设文档嵌入rag.ipynb` 总结 HyDE（Hypothetical Document Embeddings）。

流程：

```text
query
→ LLM 生成 hypothetical document
→ embed(hypothetical document)
→ vector search
→ retrieved real chunks
→ final answer based on real chunks
```

HyDE 用“可能的答案文档”提高查询表示的语义密度，缓解短 query 与长文档之间的 semantic gap。

注意：假设文档可能幻觉并带偏检索；最终回答必须强制基于真实 retrieved chunks，而不是基于假设文档本身。

## Hybrid Retrieval

`16_融合检索.ipynb` 结合向量检索的语义召回与 BM25 的关键词精确匹配。

```text
query
→ vector search 得到 dense score
→ BM25 得到 sparse score
→ 分数归一化
→ 加权融合
→ top-k 上下文
```

融合公式：

```text
combined_score = alpha * norm_vector_score + (1 - alpha) * norm_bm25_score
```

- 向量检索擅长语义相似、同义改写。
- BM25 擅长专有名词、型号、数字、代码、精确词。

中文 BM25 不能简单按空格切词，需要中文分词。固定 alpha 不适合所有 query，可与自适应检索结合。

## RRF 融合

RRF（Reciprocal Rank Fusion）只看排名，不看原始分数。

```text
score(d) = Σ 1 / (k + rank_i(d))
```

其中 `k` 常取 60。

优点：不需要校准不同召回通道的分数，适合融合向量检索、BM25、多 query 结果。

## Reranking

`8_重排序.ipynb` 总结初召回后的精排。

```text
初召回 top 20-100
→ Rerank 打分
→ 精排 top 3-5
→ 构造上下文
```

课程实现包括：

- **LLM rerank**：对每个候选 chunk 调用 LLM，输出 0-10 相关性分数。
- **关键词 rerank**：中文分词后结合原向量相似度、关键词命中、位置与频次加权。

生产中更常用专门 Cross-Encoder 或 rerank 模型，例如 BGE / GTE / Jina reranker。Rerank 更准但更慢，且不能弥补初召回完全没召回正确证据的问题。

## Relevant Segment Extraction

`9_相关段落提取.ipynb` 不返回离散 top-k chunk，而是根据相关 chunk 的连续性恢复较完整的文本段。

```text
chunk_value = relevance_score - irrelevant_chunk_penalty
```

再用类似最大子数组和的搜索，选出若干连续 segment。

适合报告、论文、法规等相关内容连续出现的长文档。风险是：如果信息分散，连续段可能带入大量无关上下文；惩罚参数过松会选太长，过紧会漏上下文。

## Context Compression

`10_上下文压缩.ipynb` 在检索后、生成前压缩上下文，只保留与 query 有关的信息。

| 类型 | 做法 | 风险 |
|---|---|---|
| selective | 选择性保留相关内容 | 仍可能遗漏 |
| summary | 面向 query 摘要 | 可能生成式偏差 |
| extraction | 逐字抽取相关句 | 保真但上下文连接弱 |

压缩率：

```text
compression_ratio = (original_length - compressed_length) / original_length
```

实践原则：不要无条件压缩；可在上下文超过窗口预算 70%-80% 时触发。高风险问答应保留原文引用，不只保留摘要。

## Embedding 与 Rerank 模型选型

第 5 课建议：

- 通用 / 中文：`bge-m3`、`bge-large-zh`
- 多语言：`gte-multilingual-base`、`bge-m3`
- 资源紧张：`e5-small`、`MiniLM`
- 长文档：`Jina Embeddings v2`
- Rerank：`bge-reranker-base/large`、`gte-multilingual-reranker`、`MiniLM Cross-Encoder`、`Jina ColBERT`

经典组合：

```text
BGE / E5 / GTE 初召回 top 100
→ BGE-reranker / GTE-reranker 精排 top 5
```

## 原始资料

- `从零-RAG大师/7_查询转换.ipynb`
- `从零-RAG大师/8_重排序.ipynb`
- `从零-RAG大师/9_相关段落提取.ipynb`
- `从零-RAG大师/10_上下文压缩.ipynb`
- `从零-RAG大师/16_融合检索.ipynb`
- `从零-RAG大师/19_假设文档嵌入rag.ipynb`
- `第5课-RAG (Part 2)/【RAG实战-11】Embeding model和Rerank model怎么选.pdf`

## 关联连接

- [[RAG]] — RAG 总览
- [[ragChunking]] — Chunking 决定召回上限
- [[ragVectorDatabases]] — 向量数据库与索引
- [[ragIndustrialPractice]] — 在线召回工程实现
- [[ragEvaluation]] — Recall@K、MRR、Faithfulness 等评估
- [[LLMInference]] — 推理延迟与首字响应
