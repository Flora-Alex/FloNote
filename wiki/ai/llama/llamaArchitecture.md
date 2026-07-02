---
type: knowledge
tags: [AI, Llama, Llama3, Transformer, 架构]
article_id: OBA-llama-architecture
created_at: 2026/06/23
updated_at: 2026/06/23
---

# Llama 3 架构

[[llamaArchitecture]] 总结 Llama 3 的核心结构。它本质上是现代 Decoder-only [[transformer/Transformer]]：用 [[transformer/inferenceOptimization|RoPE + GQA]] 做自注意力，用 RMSNorm 做归一化，用 SwiGLU 做 FFN，用 causal mask 做自回归生成。

## 整体结构

Llama 3 的一层 block：

```text
x
→ RMSNorm(x)
→ GQA Self-Attention + RoPE
→ x + attention_output
→ RMSNorm
→ SwiGLU FFN
→ residual add
```

完整推理链路：

```text
Token IDs
→ Token Embedding
→ 32 × Llama Block
→ Final RMSNorm
→ Output Weight
→ Vocab Logits
```

## Pre-Norm 结构

Llama 3 使用 pre-norm：先归一化，再进入 attention 或 FFN。

```text
x = x + Attention(RMSNorm(x))
x = x + FFN(RMSNorm(x))
```

相比 post-norm，pre-norm 更适合深层 Transformer 的稳定训练。

## RMSNorm

notebook 中的实现：

```python
def rms_norm(tensor, norm_weights):
    return (tensor * torch.rsqrt(tensor.pow(2).mean(-1, keepdim=True) + norm_eps)) * norm_weights
```

数学形式：

```text
RMSNorm(x) = x / sqrt(mean(x^2) + eps) * gamma
```

与 LayerNorm 的区别：

- LayerNorm 会减均值、除标准差。
- RMSNorm 不减均值，只按均方根缩放。
- RMSNorm 计算更轻，Llama 系列常用。

## RoPE

Llama 3 使用 RoPE 给 Q/K 注入位置信息。

核心点：

- RoPE 作用在 Q 和 K 上。
- V 不做 RoPE。
- 将 head_dim 按二维 pair 分组。
- 每个二维 pair 按位置角度旋转。
- QK 点积自然携带相对位置信息。

二维旋转：

```text
x' = x cosφ - y sinφ
y' = x sinφ + y cosφ
```

Llama 3 8B 中：

```text
rope_theta = 500000
head_dim = 128
```

## GQA

Llama 3 8B 的 attention 配置：

```text
n_heads = 32
n_kv_heads = 8
```

所以：

```text
group_size = 32 / 8 = 4
```

即每 4 个 Query heads 共享 1 组 K/V。

权重形状体现了这一点：

| 权重 | 形状 | 说明 |
|---|---|---|
| `Wq` | `[4096, 4096]` | 32 个 Q heads |
| `Wk` | `[1024, 4096]` | 8 个 K heads |
| `Wv` | `[1024, 4096]` | 8 个 V heads |
| `Wo` | `[4096, 4096]` | 输出投影 |

GQA 的价值：

- 降低 KV Cache 显存。
- 提高长上下文推理吞吐。
- 相比 MQA 保留更多表达能力。

## SwiGLU FFN

Llama 3 的 FFN 不是普通：

```text
Linear → GELU → Linear
```

而是 SwiGLU：

```text
FFN(x) = W2(SiLU(xW1) ⊙ xW3)
```

notebook 对应权重：

- `feed_forward.w1.weight`
- `feed_forward.w2.weight`
- `feed_forward.w3.weight`

含义：

- `w1`：门控分支之一。
- `w3`：另一条升维分支。
- `w2`：降维回 hidden size。
- `⊙`：逐元素乘法。

SwiGLU 的直觉：让 FFN 通过门控机制动态选择哪些通道通过。

## Causal Self-Attention

Llama 3 是自回归模型，因此当前位置不能看到未来 token。

Attention 公式：

```text
Attention(Q,K,V) = softmax(QK^T / sqrt(d_k) + mask) V
```

其中 mask 是上三角 `-inf`，softmax 后未来位置概率为 0。

## 最终输出层

推理结束后：

```text
final_hidden = RMSNorm(last_layer_output)
logits = final_hidden[-1] @ output.weight.T
```

`logits` 维度等于词表大小：

```text
[128256]
```

然后用 greedy、temperature、top-k、top-p 等策略选出下一个 token，详见 [[transformer/inferenceOptimization|transformerDecodingSampling]]。

## 关联连接

- [[llamaOverview]] — Llama 3 总览
- [[llamaFromScratch]] — 从零推理实现
- [[llamaTokenizer]] — tokenizer 与 special tokens
- [[transformer/inferenceOptimization|transformerGqaRope]] — GQA 与 RoPE 通用实现
- [[transformer/inferenceOptimization|transformerKvCache]] — KV Cache
- [[transformer/Transformer]] — Transformer 主页面

## 原始资料

- `raw/大模型学习/第2课-llama3-from-scratch/llama3-from-scratch-Copy1.ipynb`
- `raw/大模型学习/第2课-llama3-from-scratch/第三周：手撕 Llama.pdf`
- `raw/大模型学习/第2课-llama3-from-scratch/第2课-llama3技术报告.pdf`
