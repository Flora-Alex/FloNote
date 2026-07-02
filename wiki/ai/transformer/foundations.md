---
type: knowledge
tags: [AI, Transformer, Tokenization, Embedding, Attention]
article_id: OBA-transformer-foundations
created_at: 2026/06/23
updated_at: 2026/06/24
---

# Transformer 基础：Tokenization、Embedding 与 Attention

[[foundations]] 汇总 Transformer 输入表示与核心注意力计算，合并原 `transformerTokenization`、`transformerEmbedding`、`transformerAttentionMechanism` 三个页面内容。

## Tokenization

Tokenizer 是文本到 token id 的桥梁：模型只能处理整数 ID，不能直接处理文字。

```text
文本 → token 序列 → token id → embedding 向量
```

### BPE

Byte Pair Encoding 是当前主流分词算法之一：

1. 初始化为字符或字节级最小单元。
2. 统计相邻 pair 的频率。
3. 反复合并最高频 pair。
4. 达到词表大小后停止。

优势：平衡词表大小和序列长度，能学习常见子词组合，如 `un`、`ing`、`tion`。

### Byte-level BPE、WordPiece、SentencePiece

- **Byte-level BPE**：从字节出发，天然覆盖所有 Unicode 字符，GPT 系列常用。
- **WordPiece**：BERT 常用，倾向选择能最大化语言模型似然的子词。
- **SentencePiece / Unigram**：不依赖空格，适合中日文等无显式空格语言。

### 中文分词难点

- 中文没有天然空格分隔。
- 常用汉字通常可作为独立 token。
- 生僻字可能被拆成多个 byte token。
- 同一中文文本在不同 tokenizer 中 token 数可能差异很大。

### 工程影响

- API 成本按 token 计费。
- 上下文窗口按 token 而不是字符计算。
- Attention 复杂度与序列长度相关，分词效率影响推理成本。

## Embedding

Embedding 将离散 token id 映射成连续稠密向量。

```text
token id → 查 embedding table → dense vector
```

### One-hot 的问题

One-hot 向量维度等于词表大小，稀疏且无法表达语义相似性。

### Word2Vec

Word2Vec 学习静态词向量：

- **Skip-gram**：用中心词预测上下文。
- **CBOW**：用上下文预测中心词。
- **负采样**：只采样少量负例，降低训练成本。

### GloVe

GloVe 基于全局共现矩阵学习词向量，强调词与词的共现统计。

### 静态嵌入 vs 动态嵌入

- 静态嵌入：一个词一个向量，无法处理一词多义。
- Transformer 动态表示：初始 token embedding 经过多层 attention 后，会根据上下文形成动态语义。

## 位置表示

Self-Attention 本身是位置盲的：打乱 token 顺序，注意力计算并不知道原始顺序。因此必须注入位置信息。

常见方式：

- **sin/cos 绝对位置编码**：原始 Transformer 使用，零参数。
- **可学习位置编码**：让模型学习每个位置向量。
- **相对位置编码**：直接建模 token 间相对距离。
- **RoPE**：用旋转方式作用在 Q/K 上，是现代 LLM 主流方案，详见 [[inferenceOptimization]]。
- **ALiBi**：在 attention score 上添加距离惩罚。

## Q、K、V

Self-Attention 中，输入序列 `X` 经过三个线性投影得到：

```text
Q = XW_Q
K = XW_K
V = XW_V
```

直觉：

- Query：当前位置想找什么信息。
- Key：每个位置能提供什么索引标签。
- Value：每个位置真正携带的内容。

## Scaled Dot-Product Attention

核心公式：

```text
Attention(Q, K, V) = softmax(QK^T / sqrt(d_k)) V
```

步骤：

1. `QK^T` 计算每对 token 的相关性。
2. 除以 `sqrt(d_k)` 稳定方差。
3. softmax 转成概率分布。
4. 对 V 加权求和，得到上下文表示。

## 为什么除以 sqrt(d_k)

当 `d_k` 较大时，Q/K 点积方差会变大，softmax 输入绝对值偏大，输出接近 one-hot，梯度变小。除以 `sqrt(d_k)` 能让 softmax 保持合适温度，稳定训练。

## Mask

### Padding Mask

屏蔽 padding token，避免模型关注无意义填充。

### Causal Mask

屏蔽未来 token，保证自回归生成时第 t 个位置只能看见 t 之前的 token。

## Multi-Head Attention

单头注意力只能学习一种关联模式；多头注意力将 Q/K/V 投影到多个子空间，分别计算 attention 后拼接。

```text
MultiHead(Q,K,V) = Concat(head_1, ..., head_h) W_O
```

不同 head 可以关注语法、语义、共指、位置等不同关系。

## Self-Attention 与 Cross-Attention

- **Self-Attention**：Q/K/V 都来自同一序列。
- **Cross-Attention**：Q 来自 Decoder，K/V 来自 Encoder 输出。

Decoder-only LLM 主要使用 masked self-attention；Encoder-Decoder 模型会使用 cross-attention。

## FFN、Residual 与 Norm

每个 Transformer block 通常包含：

```text
Attention → Residual + Norm → FFN → Residual + Norm
```

FFN 对每个位置独立做非线性变换，是模型容量和知识存储的重要来源。Residual 和 LayerNorm 则稳定深层训练。

## 原始资料

- `raw/大模型学习/第1课-Transformer/大模型基础：分词机制.pdf`
- `raw/大模型学习/第1课-Transformer/大模型基础：词嵌入.pdf`
- `raw/大模型学习/第1课-Transformer/第1课transformer课件.pdf`
- `raw/大模型学习/第1课-Transformer/attention is all you need.pdf`

## 关联连接

- [[Transformer]] — Transformer 总览
- [[inferenceOptimization]] — RoPE、GQA、KV Cache 与解码优化
- [[modernArchitectures]] — 现代 Decoder-only LLM 架构
- [[practice]] — 手写 Attention 与 Excel 手算
- [[LLM]] — 大语言模型
