---
type: knowledge
tags: [AI, RAG, GraphRAG, SelfRAG, CRAG, 多模态RAG]
article_id: OBA-rag-advanced-patterns
created_at: 2026/06/23
updated_at: 2026/06/24
---

# 高级 RAG 范式

[[ragAdvancedPatterns]] 汇总 RAG 课程中的 Feedback Loop、Adaptive Retrieval、Self-RAG、Multi-modal RAG、Graph RAG、Hierarchical RAG、Corrective RAG 与 RL-enhanced RAG。这些方法用于解决普通 RAG 在复杂问题、多跳推理、低质量检索、动态知识、多模态内容和持续优化上的不足。

## Feedback Loop RAG

`11_反馈回路机制的rag.ipynb` 把用户反馈写回 RAG 系统，用于调整排序、沉淀优质问答，并持续优化检索。

```text
用户查询 → 检索 → 生成答案 → 用户反馈
→ 反馈持久化
→ 后续检索时调整分数
→ 高质量 Q&A 可写回索引
```

适合客服、FAQ、企业知识库。风险是低质量反馈会污染系统，错误答案被高分反馈后可能被放大，因此需要时间衰减、置信度、去重与人工审核。

## Adaptive Retrieval

`12_自适应检索.ipynb` 先识别查询类型，再按类型选择不同检索策略。

| 查询类型 | 策略 |
|---|---|
| 事实性 | query rewrite + 精确检索 + rerank |
| 分析性 | 子查询分解 + 多路检索 |
| 观点性 | 多视角检索 |
| 情境性 | 上下文化查询 + 上下文相关性评分 |

注意：课程代码中存在分类返回中文、策略判断使用英文标签的问题，会导致路由失效。生产中应使用结构化枚举输出。

## Self-RAG

`13_自适应rag.ipynb` 在 RAG 流程中加入自我判断：

```text
是否需要检索？
→ 检索结果是否相关？
→ 答案是否被上下文支持？
→ 答案效用如何？
→ 选择最佳候选或 fallback
```

它适合对无依据回答敏感的系统，但会增加多次 LLM 判断成本。相关性、支持性、效用等判断必须结构化，否则容易“看似运行，实际失效”。

## Multi-modal RAG

`15_多模态rag.ipynb` 采用“图像转 caption → 文本 embedding → 统一检索”的方式，把 PDF 中的图片信息纳入 RAG。

```text
PDF → 提取文本与图片
→ 文本切块
→ 图片生成 caption
→ 文本 chunk + 图片 caption 一起 embedding
→ query 统一检索
→ 生成时标注来源类型与页码
```

适合论文图表、研报图表、产品手册、医疗报告、技术流程图。局限是 caption 可能遗漏数字、公式、坐标轴和细节；复杂图表需要 OCR、表格解析或直接 image embedding。

## Graph RAG

`17_图rag.ipynb` 把 chunk 作为图节点，通过概念重叠与语义相似度建立边，再从查询相关节点出发遍历图。

```text
edge_weight = 0.7 * embedding_similarity + 0.3 * concept_overlap_score
```

查询流程：

```text
query embedding
→ 找 top-k 初始节点
→ 沿高权重边优先遍历
→ 汇总相关 chunk
→ 生成答案
```

适合多跳问答、实体关系密集的企业知识网络、技术综述、医学、法律。风险是图构建成本高、概念抽取质量决定图质量，chunk-chunk 图不等于完整实体关系图谱。

## Hierarchical RAG

`18_分层检索rag.ipynb` 先在摘要层定位相关页面，再在这些页面的详细 chunk 中检索证据。

```text
Level 1：页级 / 章节级摘要索引
Level 2：页内 / 章节内详细 chunk 索引
```

查询流程：

```text
query
→ 检索 summary store
→ 得到相关 page / section
→ 用 page filter 限制 detailed store
→ 检索细节 chunk
→ 生成并引用页码
```

价值是长文档降噪、先粗后细。风险是摘要层漏召回会导致细节层永远检索不到。

## Corrective RAG

`20_动态纠正rag.ipynb` 先评估本地检索结果质量，再决定使用本地知识、外部搜索，或二者结合。

```text
query → 本地检索 top-k → LLM 相关性评分

if max_score > 0.7:
    使用本地文档
elif max_score < 0.3:
    执行网络搜索并精炼
else:
    本地文档 + 网络搜索结合
```

CRAG 适合本地知识缺失、过期或检索低置信场景。外部搜索会带来 source quality、合规性和可控性风险。

## RL-enhanced RAG

`21_强化学习增强rag.ipynb` 把 RAG 管道形式化为状态、动作、奖励的优化问题。

状态：

```text
original_query
current_query
context
previous_responses
previous_rewards
```

动作：

- rewrite_query：改写查询。
- expand_context：检索更多上下文。
- filter_context：过滤上下文。
- generate_response：生成回答。

奖励：答案 embedding 与标准答案 embedding 的余弦相似度。

```text
reward = cosine(embedding(response), embedding(ground_truth))
```

注意：课程实现更像启发式策略搜索，不是严格深度强化学习。embedding similarity reward 不等价于事实正确。

## 策略矩阵

| 策略 | 解决的问题 | 代价 |
|---|---|---|
| Feedback Loop | 系统越用越准 | 反馈污染风险 |
| Adaptive Retrieval | 不同问题用不同策略 | 路由错误会级联失败 |
| Self-RAG | 避免盲目检索与无依据回答 | 多次 LLM 调用 |
| Multi-modal RAG | 纳入图片、图表信息 | caption / OCR 质量瓶颈 |
| Graph RAG | 关系、多跳、路径解释 | 图构建复杂 |
| Hierarchical RAG | 长文档降噪 | 第一层摘要漏召回 |
| Corrective RAG | 本地检索失败时纠错 | 外部搜索不可控 |
| RL RAG | 策略自动优化 | reward 难设计，成本高 |

## 原始资料

- `从零-RAG大师/11_反馈回路机制的rag.ipynb`
- `从零-RAG大师/12_自适应检索.ipynb`
- `从零-RAG大师/13_自适应rag.ipynb`
- `从零-RAG大师/15_多模态rag.ipynb`
- `从零-RAG大师/17_图rag.ipynb`
- `从零-RAG大师/18_分层检索rag.ipynb`
- `从零-RAG大师/20_动态纠正rag.ipynb`
- `从零-RAG大师/21_强化学习增强rag.ipynb`
- `第5课-RAG (Part 2)/【论文精读】RAG+推理综述.pdf`

## 关联连接

- [[RAG]] — RAG 总览
- [[ragRetrievalOptimization]] — Query 和召回优化
- [[ragIndustrialPractice]] — 工业级落地场景
- [[ragEvaluation]] — Self-RAG / CRAG 需要的评估信号
- [[agent/agentArchitectures|CoT]] — RAG 与推理结合
- [[agent/agentArchitectures|ReAct]] — 检索动作与推理动作交替
- [[AIAgent]] — Agentic RAG 的上层范式
