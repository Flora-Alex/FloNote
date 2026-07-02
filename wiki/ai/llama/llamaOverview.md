---
type: summary
tags: [AI, Llama, Llama3, 大模型]
article_id: OBA-llama-overview
created_at: 2026/06/23
updated_at: 2026/06/23
---

# Llama 3 总览

[[llamaOverview]] 总结 `raw/大模型学习/第2课-llama3-from-scratch` 中 Llama 3 相关资料，是 [[llamaArchitecture]]、[[llamaTokenizer]]、[[llamaFromScratch]] 的入口页。Llama 3 是典型的 Decoder-only [[transformer/Transformer]] 语言模型，结合了 [[transformer/inferenceOptimization|GQA 与 RoPE]]、RMSNorm、SwiGLU、KV Cache 等现代 LLM 结构。

## 核心定位

Llama 3 可以理解为现代开源大模型的一种标准形态：

```text
Decoder-only Transformer
+ Causal Language Modeling
+ RMSNorm
+ RoPE
+ GQA
+ SwiGLU
+ 大词表 tokenizer
+ KV Cache 推理
```

本课重点不是训练 Llama 3，而是从已有权重出发，手动复现一次前向推理流程：

```text
文本
→ tokenizer
→ token ids
→ embedding
→ 32 层 Llama block
→ final RMSNorm
→ output projection
→ logits
→ next token
```

## Llama 3 8B 关键配置

从 `llama3-from-scratch-Copy1.ipynb` 读取到的 Llama 3 8B 参数：

| 参数 | 含义 | 数值 |
|---|---|---:|
| `dim` | hidden size | 4096 |
| `n_layers` | Transformer 层数 | 32 |
| `n_heads` | Query heads | 32 |
| `n_kv_heads` | Key/Value heads | 8 |
| `vocab_size` | 词表大小 | 128256 |
| `multiple_of` | FFN 对齐粒度 | 1024 |
| `ffn_dim_multiplier` | FFN 中间维度倍率 | 1.3 |
| `norm_eps` | RMSNorm epsilon | 1e-5 |
| `rope_theta` | RoPE base theta | 500000 |

重要推论：

- 每个 attention head 维度：`4096 / 32 = 128`。
- `n_heads=32` 且 `n_kv_heads=8`，说明 Llama 3 8B 使用 [[transformer/inferenceOptimization|GQA]]。
- 每 4 个 Query heads 共享 1 组 K/V heads。
- 大词表 `128256` 支撑更复杂的多语言、代码和 special tokens。

## 结构总览

每层 Llama block 可以概括为：

```text
x
→ RMSNorm
→ GQA Self-Attention with RoPE
→ residual add
→ RMSNorm
→ SwiGLU FFN
→ residual add
```

完整模型：

```text
Token Embedding
→ 32 × Llama Block
→ Final RMSNorm
→ LM Head
```

## 本课拆分页面

- [[llamaArchitecture]] — Llama 3 架构：RMSNorm、RoPE、GQA、SwiGLU。
- [[llamaTokenizer]] — Llama 3 tokenizer、special tokens 与 tiktoken 风格 BPE。
- [[llamaFromScratch]] — 从零手动推理 Llama 3 的代码流程。
- [[transformer/inferenceOptimization|transformerKvCache]] — 第 2 课新增的 KV Cache 通用知识。
- [[transformer/inferenceOptimization|transformerGqaRope]] — 第 2 课新增的 GQA 与 RoPE 实现细节。

## 面试速记

如果被问“Llama 3 和原始 Transformer 有哪些关键区别”，可以回答：

1. Llama 3 是 Decoder-only 架构，不是原始 Encoder-Decoder。
2. 使用 RMSNorm 替代 LayerNorm。
3. 使用 RoPE 注入位置信息，而不是输入层正余弦相加。
4. 使用 GQA 降低 KV Cache 显存。
5. 使用 SwiGLU FFN，而不是普通 ReLU/GELU FFN。
6. 使用 causal mask 做自回归预测。

## 关联连接

- [[llamaArchitecture]] — Llama 3 架构细节
- [[llamaTokenizer]] — Llama 3 tokenizer
- [[llamaFromScratch]] — 从零实现推理流程
- [[transformer/Transformer]] — Transformer 主页面
- [[transformer/inferenceOptimization|transformerGqaRope]] — GQA 与 RoPE
- [[transformer/inferenceOptimization|transformerKvCache]] — KV Cache 推理优化
- [[LLM]] — 大语言模型总体概念

## 原始资料

- `raw/大模型学习/第2课-llama3-from-scratch/第2课-llama3课件.pdf`
- `raw/大模型学习/第2课-llama3-from-scratch/第2课-llama3技术报告.pdf`
- `raw/大模型学习/第2课-llama3-from-scratch/第三周：手撕 Llama.pdf`
- `raw/大模型学习/第2课-llama3-from-scratch/llama3-from-scratch-Copy1.ipynb`
