---
type: knowledge
tags: [AI, Transformer, LLM, 注意力机制]
article_id: OBA-ai-transformer
created_at: 2026/05/15
updated_at: 2026/06/24
---

# Transformer

Transformer 是现代 [[LLM]] 的基础架构，由 Vaswani 等人在 2017 年论文 *Attention Is All You Need* 中提出。它抛弃 RNN 的顺序循环结构，完全基于注意力机制进行序列建模，成为大语言模型、多模态模型和生成式 AI 的核心范式。

## 一句话理解

> Transformer 用 Attention 让每个 token 直接和所有 token 交互，用并行矩阵计算替代 RNN 的逐步递归。

## RNN 的两个问题

1. **顺序计算难并行**：第 t 步依赖第 t-1 步，GPU 并行效率低。
2. **长距离依赖路径长**：信息经过很多步传递后衰减，即使 LSTM/GRU 也难彻底解决。

Self-Attention 将任意两个位置的依赖路径缩短为 1，从而提升长距离建模能力与训练效率。

## Transformer 全链路

```text
文本
→ Tokenization
→ Token ID
→ Embedding + Position Encoding
→ Q / K / V
→ Scaled Dot-Product Attention
→ Multi-Head Attention
→ Add & Norm
→ FFN
→ 多层堆叠
→ Linear + Softmax
→ 自回归生成 / 理解任务
```

## 原始 Encoder-Decoder 架构

原始 Transformer 由 Encoder 和 Decoder 组成：

- **Encoder**：Self-Attention + FFN，用于双向理解输入。
- **Decoder**：Masked Self-Attention + Cross-Attention + FFN，用于自回归生成输出。
- **Cross-Attention**：Decoder 查询 Encoder 输出，适合机器翻译、摘要等输入输出序列任务。

## 三种架构变体

| 架构 | 代表模型 | 注意力方式 | 预训练目标 | 擅长任务 |
|---|---|---|---|---|
| Encoder-only | BERT | 双向注意力 | MLM | 分类、NER、检索、理解 |
| Decoder-only | GPT、Claude、Qwen、Llama | 单向因果注意力 | CLM / next-token prediction | 生成、对话、推理 |
| Encoder-Decoder | T5、BART | Encoder 双向 + Decoder 单向 | Seq2Seq | 翻译、摘要、结构化生成 |

## 为什么 Decoder-only 成为主流

当前主流 LLM 几乎全部采用 Decoder-only，原因是：

1. **目标简单**：预测下一个 token 是统一、稳定、可扩展的自监督目标。
2. **任务可统一为续写**：问答、翻译、总结、代码生成都能表达为“继续生成”。
3. **规模效应稳定**：参数量、数据量、算力扩大时涌现能力更明显。
4. **推理天然匹配 KV Cache**：自回归生成可缓存历史 K/V，减少重复计算。

## 论文核心贡献

*Attention Is All You Need* 的核心贡献：

1. 完全基于 Attention 的序列建模，不再依赖 RNN/CNN。
2. 提出 Scaled Dot-Product Attention。
3. 提出 Multi-Head Attention。
4. 使用 Positional Encoding 注入顺序信息。
5. 使用 Position-wise FFN、Residual Connection、LayerNorm 组成可堆叠模块。
6. 在机器翻译任务上取得更好效果，并显著提升训练并行度。

## 高频面试点

- Q/K/V 分别是什么？
- 为什么 attention 分数要除以 `sqrt(d_k)`？
- 为什么 Self-Attention 本身位置盲？
- Self-Attention 和 Cross-Attention 有什么区别？
- Multi-Head Attention 为什么有效？
- Causal Mask 和 Padding Mask 分别解决什么问题？
- FFN 为什么叫 position-wise？
- Transformer 的主要缺点是什么？

## 课程资料脉络

`raw/大模型学习/第1课-Transformer` 的资料被压缩为 5 个页面：

- [[foundations]] — Tokenization、Embedding、位置编码、Attention 基础。
- [[inferenceOptimization]] — MQA、GQA、MLA、RoPE、KV Cache、Flash Attention、解码采样。
- [[modernArchitectures]] — Decoder-only 演进、现代 LLM block、MoE、Reasoning 趋势。
- [[practice]] — PyTorch 手写实现、Excel 手算材料、学习路径。
- 本页 — Transformer 总览、架构变体、论文贡献、学习入口。

## 原始资料

- `raw/大模型学习/第1课-Transformer/第1课transformer课件.pdf`
- `raw/大模型学习/第1课-Transformer/第二周：手撕Transformer & MOE.pdf`
- `raw/大模型学习/第1课-Transformer/attention is all you need.pdf`
- `raw/大模型学习/第1课-Transformer/【论文精读 & 面试题】Transformer论文逐段精读.pdf`

## 关联连接

- [[LLM]] — 基于 Transformer Decoder-only 架构的大规模语言模型
- [[MoE]] — 混合专家架构，在 Transformer FFN 层引入稀疏路由
- [[LLMTraining]] — Transformer 模型的训练流程与技巧
- [[LLMInference]] — 推理、KV Cache 与部署优化
- [[foundations]] — Transformer 输入表示与 Attention 基础
- [[inferenceOptimization]] — 推理优化与解码采样
- [[modernArchitectures]] — 现代 LLM 架构演进
- [[practice]] — 代码实践与手算材料
