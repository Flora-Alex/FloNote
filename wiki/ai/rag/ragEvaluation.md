---
type: knowledge
tags: [AI, RAG, 评估, 测试集, Faithfulness]
article_id: OBA-rag-evaluation
created_at: 2026/06/23
updated_at: 2026/06/24
---

# RAG 评估与测试集构建

[[ragEvaluation]] 总结 RAG 课程中的评估体系。RAG 评估不能只看最终答案“像不像”，必须拆成检索、生成、证据、性能、用户体验五层，并构建带标准答案和支持证据的测试集。

## 评估分层

```text
检索层：找没找到正确证据
生成层：答没答对问题
证据层：答案是否被上下文支持
性能层：响应是否足够快
体验层：用户是否满意
```

## 检索层指标

### Precision@K

前 K 个检索结果中，相关结果的比例。

```text
Precision@K = relevant_in_top_k / K
```

### Recall@K

标准证据中有多少被 top K 覆盖。

```text
Recall@K = retrieved_gold_evidence / all_gold_evidence
```

### MRR

Mean Reciprocal Rank，衡量第一个相关结果排得有多靠前。

```text
MRR = mean(1 / rank_first_relevant)
```

### MAP / nDCG / 冗余率

- MAP：衡量多个相关文档的整体排序质量。
- nDCG：考虑相关性等级的排序质量。
- 冗余率：检索结果是否重复表达同一信息，冗余过高会浪费上下文窗口。

### Context Sufficiency

检索上下文是否足以回答问题。这是连接检索层和生成层的关键指标。

## 生成层指标

- **Correctness / Accuracy**：答案是否正确、完整、对题。
- **Faithfulness**：答案中的事实是否都能被检索上下文支持。
- **Answer Relevancy**：答案是否真正回答了用户问题。
- **Completeness**：答案是否覆盖问题所需关键点。

Faithfulness 是 RAG 防幻觉的核心指标。

## 证据层指标

工业级 RAG 应要求答案可追溯：

- 是否给出引用。
- 引用是否真实存在。
- 引用是否支持对应结论。
- 证据是否来自正确文档、页码、chunk。
- 引用和结论是否一一对应。

## 性能指标

指标：

- 平均响应时间。
- 首字响应时间。
- P95 / P99 延迟。
- QPS / 吞吐量。
- CPU / 内存 / 网络 IO。
- 不同数据规模下的检索耗时。

性能评估应拆分：

```text
Ttotal = Tembed(query) + Tretrieval + Tprompt-build + TLLM-first-token + Tinfra-overhead
```

## 用户体验指标

线上指标：

- 用户满意度。
- 点踩率。
- 追问率。
- 转人工率。
- 空回答率。
- 答案可读性。

## 测试集构建流程

### 1. 确定评估范围

明确领域和任务：医疗、法律、金融、教育、企业知识库、客服等。

### 2. 设计问题集

问题类型应覆盖：

- 简单事实题。
- 多跳问题。
- 是非判断题。
- 原因解释题。
- 流程步骤题。
- 跨文档综合题。
- 反事实 / 不可回答问题。
- 时效性问题。
- 多模态问题。

### 3. 生成问题

两种方式：

- 人工编写：质量高、成本高。
- LLM 生成：效率高，但需人工审核。

推荐：LLM 初生成 + 人工筛选修订。

### 4. 标注标准答案与支持证据

每条样本至少包含：

```text
query
reference_answer
gold_evidence_doc_id
gold_evidence_chunk_id
gold_page_or_section
question_type
difficulty
```

### 5. 审查与平衡

检查：

- 问题是否重复。
- 答案是否泄漏在问题中。
- 难度是否均衡。
- 领域覆盖是否均衡。
- 证据是否唯一或可替代。

## RAGAs 相关指标

常用：

- Faithfulness
- Answer Relevancy
- Context Recall
- Context Precision

这些指标适合自动化评估，但关键场景仍需人工抽检。

## 高级策略的评估重点

| 策略 | 重点指标 |
|---|---|
| 语义分块 / 命题分块 | Recall@K、Context Sufficiency、证据粒度 |
| Query Rewrite / HyDE | 召回提升、查询漂移率 |
| Hybrid Search | Recall@K、MRR、专有名词命中率 |
| Rerank | MRR、nDCG、top-k 相关性 |
| Context Compression | 压缩率、Faithfulness、信息损失率 |
| Self-RAG / CRAG | 拒答准确率、低置信识别、Source Quality |
| Graph RAG | 多跳召回、路径可解释性 |
| Multi-modal RAG | 图表答案准确率、caption 支持度 |
| RL RAG | reward 与人工评分相关性、泛化能力 |

## 防幻觉评估

RAG 幻觉来源：

1. 检索没找到正确证据。
2. 检索结果相关但不充分。
3. LLM 在上下文外补充推断。
4. 引用错位。
5. 多个证据冲突。
6. 外部搜索或反馈闭环引入低质量信息。

防控方式：

- Rerank 分数低于阈值拒答。
- Prompt 要求只基于资料回答。
- 每条结论附引用。
- 生成后做引用核查。
- 对不可回答问题进行专门评估。
- 对 Self-RAG / CRAG 的质量门控单独评估。

## 实现陷阱清单

- 单次 LLM-as-judge 不稳定，应多样本、多指标、人工抽检。
- 用 embedding similarity 当 reward 可能高估事实错误但语义接近的答案。
- 自适应检索和 Self-RAG 中，LLM 输出标签必须结构化枚举，避免中英文标签不一致。
- 上下文压缩评估不能只看 token 降低，还要看证据保真。
- HyDE 评估要监控“查询漂移”和假设文档幻觉。
- 外部搜索增强要评估 source quality 与合规风险。

## 原始资料

- `第5课-RAG (Part 2)/【RAG实战-第6天】RAG评估.docx`
- `第5课-RAG (Part 2)/【RAG实战-13】 系统评估测试集构建指南.pdf`
- `从零-RAG大师/3_切块大小选择.ipynb`
- `从零-RAG大师/8_重排序.ipynb`
- `从零-RAG大师/10_上下文压缩.ipynb`
- `从零-RAG大师/13_自适应rag.ipynb`
- `从零-RAG大师/20_动态纠正rag.ipynb`
- `从零-RAG大师/21_强化学习增强rag.ipynb`

## 关联连接

- [[RAG]] — RAG 总览
- [[ragRetrievalOptimization]] — 检索优化会影响 Recall@K / MRR
- [[ragAdvancedPatterns]] — 高级范式需要质量门控评估
- [[ragIndustrialPractice]] — 工业级系统指标
- [[ragVectorDatabases]] — 向量库性能影响检索延迟
- [[AIHallucination]] — 幻觉问题
