---
type: knowledge
tags: [AI, Transformer, DecoderOnly, MoE, LLM架构]
article_id: OBA-transformer-modern-architectures
created_at: 2026/06/23
updated_at: 2026/06/24
---

# 现代 Transformer 架构演进

[[modernArchitectures]] 汇总 Decoder-only 演进、现代 LLM block、MoE、长上下文和 reasoning 模型趋势。

## Decoder-only 现代范式

```text
Decoder-only Transformer
+ Causal Mask
+ Next-token Prediction
+ 大规模预训练
+ 指令微调 / 偏好对齐 / 强化学习
```

它将几乎所有 NLP 任务统一为“根据上下文继续生成”。

## GPT 范式演进

### GPT-1 到 GPT-3

- GPT-1：预训练 + 微调。
- GPT-2：zero-shot 生成能力开始显现。
- GPT-3：few-shot / in-context learning 成为核心能力。

启发：模型规模、数据规模、计算规模扩大后，next-token prediction 可以涌现出通用能力。

### InstructGPT / ChatGPT

- Base model 学语言和世界知识。
- Instruct / Chat model 学指令遵循、人类偏好和对话格式。
- 后训练包括 SFT、Reward Model、RLHF / PPO / DPO 等。

## 现代开源 LLM 的共同 Block

Llama、Qwen、Mistral、DeepSeek 等现代模型通常采用：

```text
Token Embedding
→ RMSNorm
→ Self-Attention with RoPE + GQA / MLA
→ Residual
→ RMSNorm
→ SwiGLU FFN / MoE FFN
→ Residual
→ Final Norm
→ LM Head
```

共性：

- Decoder-only。
- RoPE。
- GQA 或 MLA。
- RMSNorm 替代 LayerNorm。
- SwiGLU / gated FFN。
- 多语言 tokenizer。
- 长上下文扩展。

## Qwen / Llama 的共性

- 都采用 Decoder-only。
- 都使用 RoPE 或其变体。
- 都支持长上下文扩展。
- 都在后训练中强化指令遵循和对话能力。
- 工程上都重视 KV Cache、GQA、Flash Attention 等推理效率。

## MoE

MoE（Mixture of Experts）通常把 dense FFN 替换为多个专家 FFN，并通过 Router/Gate 为每个 token 选择少数专家。

```text
token hidden state
→ router 打分
→ Top-K experts
→ 专家 FFN 计算
→ 加权合并
```

### Expert Networks

专家通常是 FFN，每个专家参数不同。MoE 的目标是增加总参数容量，但每个 token 只激活部分参数。

### Router / Gate

Router 为每个 token 对所有专家打分，并选择 Top-K 专家。

- Soft Gating：所有专家参与，成本高。
- Hard Gating：只选择少数专家，稀疏激活。

### 负载均衡

如果 Router 总是选择少数专家，会造成专家拥塞和训练不稳定。常见方法：

- Load Balancing Loss。
- Noisy Top-K。
- Expert Capacity。
- Router z-loss。

### 工程挑战

- all-to-all 通信。
- 专家并行。
- 不规则矩阵计算。
- batch 内 token 分发和回收。
- 推理时延迟与吞吐优化。

## 长上下文趋势

长上下文能力来自多层优化：

- RoPE scaling。
- Sliding Window Attention。
- GQA / MLA 降低 KV Cache。
- PagedAttention 和 prefix cache 等推理系统优化。
- 数据层面加入长文本训练和检索增强。

## Reasoning 模型趋势

Reasoning 模型的重点不只是改 Transformer block，而是强化后训练和推理时计算：

- 更长 chain-of-thought。
- 可验证任务上的 RL。
- 拒绝采样与蒸馏。
- 推理时计算扩展。

## 架构演进速记

```text
原始 Transformer
→ Decoder-only GPT
→ 指令模型 / Chat 模型
→ RoPE + RMSNorm + SwiGLU + GQA
→ KV Cache / Flash Attention / 长上下文
→ MoE / MLA
→ Reasoning 模型
```

## 原始资料

- `raw/大模型学习/第1课-Transformer/第二周：手撕Transformer & MOE.pdf`
- `raw/大模型学习/第1课-Transformer/大模型基础：moe.pdf`
- `raw/大模型学习/第2课-Llama3 from scratch/`
- `raw/大模型学习/第3课-DeepSeek/`
- `wiki/ai/transformer/decoderOnlyEvolution.md` 旧版内容
- `wiki/ai/transformer/transformerMoEArchitecture.md` 旧版内容

## 关联连接

- [[Transformer]] — Transformer 总览
- [[foundations]] — Attention、Embedding、位置编码基础
- [[inferenceOptimization]] — GQA、RoPE、KV Cache、MLA
- [[MoE]] — 混合专家模型主页面
- [[llama/llamaArchitecture]] — Llama 架构
- [[deepseek/deepseekArchitecture]] — DeepSeek 架构
- [[deepseek/deepseekR1]] — DeepSeek-R1 与推理模型
