---
type: knowledge
tags: [AI, RAG, LlamaIndex, QueryEngine, Retriever]
article_id: OBA-rag-llamaindex
created_at: 2026/06/23
updated_at: 2026/06/24
---

# RAG LlamaIndex 组件

[[ragLlamaIndex]] 总结 RAG 课程中 LlamaIndex 相关内容。LlamaIndex 是面向 LLM 应用的数据框架，用于连接数据源、构建索引、检索和生成回答。

## LlamaIndex 的定位

LlamaIndex 解决的问题：

```text
外部数据源
→ Document
→ Node / Chunk
→ Index
→ Retriever
→ Query Engine
→ Response
```

它让 RAG 工程从手写 pipeline 变成组件化框架。

## 最小流程

典型四步：

```python
from llama_index.core import SimpleDirectoryReader, VectorStoreIndex

documents = SimpleDirectoryReader("data").load_data()
index = VectorStoreIndex.from_documents(documents)
query_engine = index.as_query_engine()
response = query_engine.query("问题")
```

对应流程：

```text
读取数据
→ 建向量索引
→ 创建查询引擎
→ 查询生成答案
```

## Data Connectors

数据连接器负责把外部数据读成 LlamaIndex 的 Document。

课程中出现或图示涉及：

- `SimpleDirectoryReader`
- `PyMuPDFReader`
- `BeautifulSoupWebReader`
- `LlamaParse`
- DatabaseReader
- APIReader
- WebPageReader
- CustomReader

数据源：PDF、TXT、CSV、JSON、网页、数据库、API。

## Node Parser / Chunking

Node Parser 将 Document 拆成 Node。

常见组件：

- `SentenceSplitter`

作用：控制 chunk size、overlap、保留 metadata，并为后续 embedding 和 index 构建准备输入。

## Index 类型

LlamaIndex 支持多种索引：

- `VectorStoreIndex`：向量语义检索。
- `SummaryIndex`：摘要型索引。
- `KnowledgeGraphIndex`：图谱索引。
- `DocumentSummaryIndex`：文档摘要索引。
- `TreeIndex`：树状聚合索引。
- `ListIndex`：顺序遍历型索引。
- `KeywordTableIndex`：关键词表索引。

选择索引取决于任务：语义问答优先 VectorStoreIndex，全局总结可考虑 Summary / Tree，实体关系可考虑 KnowledgeGraph。

## 模型配置

LlamaIndex 可通过 `Settings` 配置默认模型：

```python
Settings.llm = OpenAI(model="gpt-4o")
Settings.embed_model = OpenAIEmbedding(model="text-embedding-3-small")
```

这对应 RAG 的两个模型角色：

- LLM：负责生成、改写、总结、判断。
- Embedding：负责文档和 query 向量化。

## 向量库接入

课程中涉及：

- `FaissVectorStore`
- `StorageContext`
- `faiss.IndexFlatL2`
- `VectorStoreIndex`

作用：让 LlamaIndex 使用外部向量索引，而不是只用内存结构。

## Retriever

常见 retriever：

- `VectorIndexRetriever`：向量检索。
- `BM25Retriever`：关键词检索。

可组合成混合检索，再交给 [[ragRetrievalOptimization|Rerank 或 Fusion]]。

## Query Engine

Query Engine 是查询编排层。

典型过程：

```text
User Query
→ Query Transformation
→ Query Routing / Rewriting / Planning
→ Retriever
→ Document Store / Vector DB
→ Retrieved Documents
→ Response Synthesis
→ Final Response
```

它不只是“查向量库”，还包括 query 规划、检索器选择、结果合成。

## Memory、Filters 与 Tools

课程中还出现：

- `ChatMemoryBuffer`：聊天记忆。
- `MetadataFilters`：metadata 条件过滤。
- `FunctionTool`：把函数封装成工具。

这些组件连接到更高级的 Agentic RAG：模型可以根据任务调用检索、工具或外部 API。

## 与手写 RAG 的关系

手写 RAG 有助于理解原理；LlamaIndex 有助于工程化：

| 维度 | 手写 RAG | LlamaIndex |
|---|---|---|
| 学习价值 | 高 | 中 |
| 开发效率 | 低 | 高 |
| 可控性 | 高 | 中 |
| 工程组件 | 需自己实现 | 内置丰富 |
| 适合 | 学原理、定制化 | 快速构建应用 |

## 原始资料

- `提示词工程+Functioncall+RAG/03_基础RAG概念与llamaindex实现.ipynb`
- `提示词工程+Functioncall+RAG/04_llamaindex组件.ipynb`
- `提示词工程+Functioncall+RAG/llamaindex-data-connectors.svg`
- `提示词工程+Functioncall+RAG/llamaindex-data-indexes.svg`
- `提示词工程+Functioncall+RAG/llamaindex-full-architecture.svg`
- `提示词工程+Functioncall+RAG/llamaindex-query-engine-svg (1).svg`

## 关联连接

- [[RAG]] — RAG 总览
- [[ragChunking]] — Document 到 Node 的切分
- [[ragRetrievalOptimization]] — Retriever 与 Query Transform
- [[ragIndustrialPractice]] — 工业级项目结构
- [[agent/agentToolUse|ToolCalling]] — FunctionTool 与工具调用
- [[AIAgent]] — Agentic RAG
