---
type: knowledge
tags: [AI, Transformer, 推理优化, KVCache, RoPE, GQA]
article_id: OBA-transformer-inference-optimization
created_at: 2026/06/23
updated_at: 2026/06/24
---

# Transformer 推理优化

[[inferenceOptimization]] 汇总现代 Transformer 推理效率相关主题，合并 MQA/GQA/MLA、RoPE、KV Cache、Flash Attention、解码采样等内容。

## 标准 MHA 的瓶颈

标准 Multi-Head Attention 在推理部署中面临三类瓶颈：

1. Attention 矩阵复杂度为 `O(N^2)`。
2. 自回归生成如果不缓存，会重复计算历史 token 的 K/V。
3. KV Cache 显存随层数、batch、上下文长度和 KV heads 线性增长。

## KV Cache

自回归生成时，第 t 步只新增一个 token，但历史 token 的 K/V 不变。因此可以缓存每层历史 K/V。

```text
prefill：一次性计算 prompt 的 K/V 并缓存
decode：每步只计算新 token 的 Q/K/V，将新 K/V 追加到 cache
```

### Cache 存什么

- 缓存每层的 K 和 V。
- 不缓存 Q，因为 Q 只用于当前 token 查询历史 K/V。

### 显存公式

```text
KV Cache = 2 × num_layers × batch_size × num_kv_heads × seq_len × head_dim × dtype_size
```

其中 `2` 代表 K 和 V。

KV Cache 是空间换时间：显著减少计算，但占用大量显存。

## MHA、MQA、GQA、MLA

| 机制 | K/V 共享方式 | 优点 | 代价 |
|---|---|---|---|
| MHA | 每个 head 独立 K/V | 表达力强 | KV Cache 最大 |
| MQA | 所有 Q heads 共享一组 K/V | KV Cache 最小，速度快 | 质量可能下降 |
| GQA | 多个 Q heads 分组共享 K/V | 质量接近 MHA，显存接近 MQA | 需要选组数 |
| MLA | 缓存低维 latent，再恢复 K/V | 进一步压缩 KV Cache | 实现复杂 |

GQA 是 Llama、Qwen、Mistral 等模型的常见选择；MLA 是 DeepSeek V2/V3 的关键优化之一。

## RoPE

RoPE（Rotary Position Embedding）将位置信息编码为 Q/K 向量的旋转角度，使 QK 点积天然包含相对位置信息。

关键点：

- RoPE 作用在 Q/K 上，不作用在 V 上。
- 零参数。
- 能表达相对位置。
- 与 KV Cache 兼容，但必须使用正确 position id。
- 已成为 Llama、Qwen、DeepSeek、Mistral 等开源 LLM 的事实标准。

## 长上下文外推

当模型处理比训练时更长的上下文时，可使用：

- **Position Interpolation**：压缩位置空间。
- **NTK-aware Scaling**：调整 RoPE 频率基数。
- **YaRN**：结合 NTK 缩放和注意力温度调节。
- **Sliding Window Attention**：只关注固定窗口，降低长上下文复杂度。

## Flash Attention

Flash Attention 的核心是分块计算 + 在线 softmax，在 GPU SRAM 中完成局部计算，减少 HBM 显存读写。

特点：

- 显存从 `O(N^2)` 降到 `O(N)`。
- 速度通常提升 2-4 倍。
- 数学上等价于标准 attention。
- 可与 GQA、RoPE、KV Cache 叠加使用。

## 解码采样

解码策略不改模型参数，只改变推理阶段如何从概率分布选择下一个 token。

### Greedy Decoding

每步选择概率最高 token。稳定、便宜，但容易重复和缺乏创造性。

### Beam Search

保留多个候选序列。适合翻译等确定性任务，不太适合开放对话。

### Temperature

调整 logits 分布的尖锐程度：

- 低 temperature：更确定。
- 高 temperature：更随机。

### Top-K Sampling

只在概率最高的 K 个 token 中采样。

### Top-P / Nucleus Sampling

选择累计概率达到 P 的最小 token 集合，再采样。比 Top-K 更自适应。

### 常见组合

```text
temperature + top_p + repetition_penalty
```

## 推理优化速记

```text
训练效率：Flash Attention
生成速度：KV Cache
显存优化：MQA / GQA / MLA
长上下文：RoPE Scaling / Sliding Window
输出质量：Temperature / Top-K / Top-P
```

## 原始资料

- `raw/大模型学习/第1课-Transformer/大模型基础：MHA&GQA&MLA.pdf`
- `raw/大模型学习/第2课-Llama3 from scratch/`
- `wiki/ai/transformer/transformerAttentionOptimization.md` 旧版内容
- `wiki/ai/transformer/transformerGqaRope.md` 旧版内容
- `wiki/ai/transformer/transformerKvCache.md` 旧版内容
- `wiki/ai/transformer/transformerDecodingSampling.md` 旧版内容

## 关联连接

- [[Transformer]] — Transformer 总览
- [[foundations]] — Attention 基础
- [[modernArchitectures]] — Llama、Qwen、DeepSeek 等现代架构
- [[LLMInference]] — LLM 推理与部署
- [[deepseek/deepseekArchitecture]] — DeepSeek MLA 与 MoE
