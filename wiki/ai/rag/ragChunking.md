---
type: knowledge
tags: [AI, RAG, Chunking, 文档解析, Embedding]
article_id: OBA-rag-chunking
created_at: 2026/06/23
updated_at: 2026/06/24
---

# RAG 文档解析与 Chunking

[[ragChunking]] 汇总 RAG 课程中关于文档处理、分块与索引增强的内容。Chunking 的质量决定 [[RAG]] 的召回上限：如果文档解析和分块阶段丢失语义，后续 embedding、检索和 rerank 很难完全补救。

## 为什么不能整篇文档入库

不能把整篇文档直接 embedding 的原因：

1. Embedding 模型有输入长度限制。
2. 长文档压成一个向量会平均掉细节。
3. 检索命中整篇文档会给 LLM 带来大量噪声。
4. 无法精确引用页码、段落和证据。

一个 chunk 通常包含：

- 向量：用于检索。
- 原文：用于生成。
- metadata：文档名、页码、标题路径、chunk_id、bbox、更新时间等。

## 固定长度切分

按固定字符数或 token 数切分，并加入 overlap。

优点：实现简单、大小可控、适合快速 demo。

缺点：可能截断句子、表格、代码和公式，破坏语义完整性。

通常 overlap 取 chunk size 的 10%～20%，用于缓解边界信息丢失。

## Chunk size 选择

`3_切块大小选择.ipynb` 的思路是：对不同 chunk size 分别建索引，用同一批 query 检索并评估回答质量，选择召回、噪声、成本之间的折中点。

| 设置 | 优点 | 风险 |
|---|---|---|
| 小 chunk | 检索精确、噪声少 | 语义不完整、上下文不足 |
| 大 chunk | 信息完整 | embedding 表征发散、prompt 成本高、Lost in the Middle |
| overlap 大 | 缓解边界截断 | 存储与上下文冗余增加 |
| overlap 小 | 成本低 | 容易丢边界信息 |

实践建议：用真实问题集评估，不要只凭经验设置 chunk size。

## 语义分块

`2_语义分块.ipynb` 使用句子级 embedding 判断语义断点。

流程：

1. 文档切成句子。
2. 为句子生成 embedding。
3. 计算相邻句子的 cosine similarity。
4. 用 percentile、standard deviation 或 IQR 找主题变化点。
5. 按断点组合成语义 chunk。

三种断点策略：

- **Percentile**：低于某个相似度分位点则切分。
- **Standard Deviation**：低于 `mean - n * std` 则切分。
- **IQR**：低于 `Q1 - 1.5 * IQR` 则切分。

注意：简单 `split('. ')` 对中文、缩写、编号不鲁棒；逐句 embedding 成本高；阈值设置不当会过度切分。

## 上下文增强检索

`4_上下文增强检索的rag.ipynb` 的核心思想是：检索命中某个 chunk 后，把它的前后邻居一起放入上下文。

```text
query → 找最相似 chunk → 取前后 context_size 个邻居 → 拼接为上下文 → 生成答案
```

价值：补齐被切断的定义、解释、例子和结论；适合章节型文档、教材、报告、政策文件。

风险：邻居不一定相关，可能引入噪声；如果相关信息分散在多处，邻近扩展不一定有效。

## Contextual Chunk Headers

`5_上下文片段标题提取.ipynb` 为每个 chunk 生成标题或摘要头，将标题 embedding 与正文 embedding 一起用于检索。

```text
score = (sim(query, chunk_text) + sim(query, header)) / 2
```

价值：让短 chunk 带有主题信息，缓解切块后章节上下文丢失。

注意：若原文已有结构化标题，应优先使用真实标题路径，而不是重新生成；标题质量差会误导检索。

## Question-Augmented RAG

`6_文档增强的rag.ipynb` 为每个 chunk 生成若干“可由该 chunk 回答的问题”，并把这些问题也写入向量库。

```text
chunk
→ LLM 生成多个问题
→ chunk embedding + question embedding 同时入库
→ query 检索时可能命中生成问题
→ 通过 metadata 回溯原始 chunk
→ 用原始 chunk 生成答案
```

关键原则：**问题只用于召回，生成时仍应回到原文 chunk**。

适合 FAQ、客服、政策问答；代价是向量库体积扩大，且生成问题可能引入伪知识。

## 命题分块

`14_命题分块.ipynb` 将文档拆成原子化、自包含、事实性的命题，并以命题为检索单元。

命题要求：

- 一条命题只表达一个事实。
- 自包含，不依赖上下文代词。
- 包含必要时间、条件和限定词。
- 尽量保持一个主谓关系。

流程：

```text
原文 chunk
→ LLM 抽取命题
→ 对命题做准确性 / 清晰度 / 完整性 / 简洁性评分
→ 过滤低质量命题
→ 命题索引 + 原 chunk 索引对比检索
```

适合事实密集型文档，例如法规、政策、财报、研究报告。风险是预处理成本高，且命题可能丢失段落上下文和论证链。

## Parent-Child Chunking

核心思想：

> 小块用于检索，大块用于生成。

实现：

- child chunk：粒度小，适合精准向量检索。
- parent chunk：包含更多上下文，适合放入 prompt。
- child 与 parent 通过 ID 关联。

这是生产级 RAG 的常见策略，也和分层检索、标题路径、metadata 设计紧密相关。

## PDF / OCR / 表格解析

工业级 RAG 的文档解析通常不只是 `read pdf`。

推荐流水线：

```text
文档类型识别
→ 文本层抽取
→ OCR 兜底
→ Layout 分析
→ 表格识别
→ 文本合并
→ 元数据保留
→ Chunking
→ Embedding 入库
```

特别注意：

- 表格要保留行列结构，可转 Markdown / HTML / JSON。
- 双栏 PDF 要恢复阅读顺序。
- 扫描件需要 OCR。
- 页码、bbox、标题路径应进入 metadata。

## 原始资料

- `从零-RAG大师/1_基础rag.ipynb`
- `从零-RAG大师/2_语义分块.ipynb`
- `从零-RAG大师/3_切块大小选择.ipynb`
- `从零-RAG大师/4_上下文增强检索的rag.ipynb`
- `从零-RAG大师/5_上下文片段标题提取.ipynb`
- `从零-RAG大师/6_文档增强的rag.ipynb`
- `从零-RAG大师/14_命题分块.ipynb`
- `第5课-RAG (Part 2)/【RAG实战-第8天】实战项目的简历准备、面试、运用（离线解析模块）.pdf`

## 关联连接

- [[RAG]] — RAG 总览
- [[ragRetrievalOptimization]] — 检索优化
- [[ragIndustrialPractice]] — 工业级离线解析模块
- [[ragEvaluation]] — chunk 质量如何评估
- [[ragLlamaIndex]] — LlamaIndex 中的 Node Parser 和 Reader
