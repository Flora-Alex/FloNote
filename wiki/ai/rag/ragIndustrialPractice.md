---
type: knowledge
tags: [AI, RAG, 工业实践, 项目实战, 后端]
article_id: OBA-rag-industrial-practice
created_at: 2026/06/23
updated_at: 2026/06/24
---

# RAG 工业级项目实战

[[ragIndustrialPractice]] 总结 RAG 第 5 课“RAG 工业级项目实战”。工业级 RAG 的重点不是跑通 demo，而是把离线解析、在线召回、服务运行、延迟优化、记忆模块、评估和项目表达做成闭环。

## 总体架构

工业级 RAG 通常分为四层：

```text
离线层：文档接入 / 解析 / Chunk / Embedding / 入库
在线层：Query / 召回 / Rerank / Prompt / 生成 / 引用
服务层：前端 / 后端 / Docker / 向量库 / Redis / API
评估层：检索指标 / 生成指标 / 证据指标 / 性能指标 / 用户反馈
```

## 离线解析模块

离线解析决定召回上限。

核心流程：

1. 文档类型识别：PDF、DOCX、图片、网页、Markdown。
2. 文本层抽取：可复制 PDF 优先走文本层。
3. OCR 兜底：扫描件、图片区域使用 OCR。
4. Layout 分析：标题、段落、表格、图片、页眉页脚、双栏。
5. 表格解析：保留行列结构，转 Markdown / HTML / JSON。
6. 文本合并：合并断行、跨页段落、去噪。
7. 元数据保留：文档名、页码、bbox、标题路径、chunk_id。
8. Chunking 与 embedding。
9. 写入向量库、元数据库和全文索引。

面试表达重点：不要说“我读取 PDF 后切块”，要说“我做了文本层抽取 + OCR 兜底 + layout 识别 + 表格结构化 + 元数据保留 + 语义 chunk”。

## 在线召回模块

在线召回决定答案质量下限。

典型流程：

```text
query 标准化
→ query rewrite / multi-query / HyDE
→ query embedding
→ dense vector recall
→ sparse BM25 recall
→ hybrid fusion / RRF
→ rerank
→ context 裁剪与去重
→ evidence 返回
```

关键点：

- 单路向量 top-k 不够。
- 专有名词、型号、数字、代码需要 BM25。
- 初召回可放大 top-k，精排后取 top 3～5。
- 返回文档名、页码、chunk_id、相似度、rerank score，便于引用。

## 前后端服务运行

第 5 课资料显示前端和后端分离。

前端：

```text
npm i --legacy-peer-deps
npm run dev
```

后端：

```text
conda create -n deepdimension python=...
conda activate deepdimension
pip install -r requirements.txt
python app_main.py
```

基础设施：

```text
docker compose -f docker-compose-base.yml up
DASHSCOPE_API_KEY=...
```

这说明项目通常包括：Python 后端、Docker 依赖服务、模型 API Key、向量库 / 缓存服务和前端页面。

## 首字响应速度优化

总耗时可拆成：

```text
Ttotal = Tembed(query) + Tretrieval + Tprompt-build + TLLM-first-token + Tinfra-overhead
```

### Embedding 阶段

- 文档 embedding 离线预计算。
- query embedding 缓存。
- 批量 embedding。
- 异步并发，避免串行 API 调用。

### 检索阶段

- 使用 ANN 索引，如 HNSW、IVF。
- Milvus collection 预加载。
- 副本提升并发。
- metadata filter 缩小搜索范围。
- 检索结果缓存。

### 生成阶段

- 流式输出，首 token 立即返回。
- Prompt 控制长度，避免塞太多无关上下文。
- 常见 FAQ 答案缓存。

### 服务层

- FastAPI + asyncio。
- 阻塞 I/O 放线程池。
- Redis 缓存 embedding / search / answer。
- P95 / P99 延迟监控。

## 记忆模块

记忆模块是动态上下文来源，不应简单把全部历史对话塞进 prompt。

推荐架构：

```text
静态知识检索 + 记忆检索
→ 融合
→ 生成
→ 记忆更新
```

记忆类型：

- 短期记忆：最近 N 轮对话。
- 长期记忆：用户偏好、画像、长期事实。
- 语义记忆：历史对话摘要 embedding 后入向量库。
- 任务记忆：当前任务状态、中间结论。

存储选型：

- Redis：短期会话记忆。
- 向量数据库：语义长期记忆。
- NoSQL / 文档库：用户画像和结构化偏好。

## 项目简历表达

可写成：

> 负责企业知识库 RAG 问答系统的离线解析与在线召回模块。离线侧实现 PDF/DOCX 文档解析、OCR 兜底、版面识别、表格结构化、语义 chunk 与元数据入库；在线侧实现 query 改写、多路召回、向量检索、BM25/Hybrid Search、rerank 精排与证据引用。通过 HNSW 索引、Embedding 缓存、检索结果缓存、流式生成与异步服务优化，将首字响应延迟降低，并用 Precision@K、Recall@K、MRR、Faithfulness、P95/P99 延迟构建评估闭环。

面试讲解四层：

1. 业务问题：为什么需要 RAG。
2. 技术架构：离线、在线、服务、评估。
3. 技术难点：PDF、表格、OCR、召回、rerank、延迟、评估。
4. 量化结果：Recall@K、MRR、P95、首字响应、满意度、幻觉率。

## 原始资料

- `第5课-RAG (Part 2)/【RAG实战-第3天】前后端服务运行.pdf`
- `第5课-RAG (Part 2)/【RAG实战-第4天】代码结构详细解析.pdf`
- `第5课-RAG (Part 2)/【RAG实战-第5天】核心代码详解.pdf`
- `第5课-RAG (Part 2)/【RAG实战-第7天】如何写一个有深度RAG项目经历.pdf`
- `第5课-RAG (Part 2)/【RAG实战-第8天】实战项目的简历准备、面试、运用（离线解析模块）.pdf`
- `第5课-RAG (Part 2)/【RAG实战-第9天】实战项目的简历准备、面试、运用（在线召回模块）.pdf`
- `第5课-RAG (Part 2)/【RAG实战-第10天】如何提升首字响应速度.pdf`
- `第5课-RAG (Part 2)/【RAG实战-12】记忆模块怎么做.pdf`

## 关联连接

- [[RAG]] — RAG 总览
- [[ragChunking]] — 离线解析与 chunking
- [[ragRetrievalOptimization]] — 在线召回和 rerank
- [[ragEvaluation]] — 工业级评估体系
- [[ragVectorDatabases]] — 向量库选型
- [[LLMInference]] — 推理延迟优化
