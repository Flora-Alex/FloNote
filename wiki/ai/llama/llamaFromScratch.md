---
type: cheatsheet
tags: [AI, Llama, Llama3, PyTorch, 推理]
article_id: OBA-llama-from-scratch
created_at: 2026/06/23
updated_at: 2026/06/23
---

# Llama 3 From Scratch

[[llamaFromScratch]] 总结 `llama3-from-scratch-Copy1.ipynb` 的手动推理流程。该 notebook 不是训练 Llama 3，而是加载 Meta-Llama-3-8B 权重，从 tokenizer 到 logits 手动走完一次 forward pass。

## 总流程

```text
加载 tokenizer 与权重
→ prompt 编码
→ token embedding
→ 逐层执行 Llama Block
→ final RMSNorm
→ output projection
→ 预测 next token
```

示例 prompt：

```text
the answer to the ultimate question of life, the universe, and everything is 
```

最终预测 token decode 为：

```text
42
```

## 1. 加载参数

notebook 读取：

```python
params.json
consolidated.00.pth
tokenizer.model
```

关键参数：

```python
dim = 4096
n_layers = 32
n_heads = 32
n_kv_heads = 8
vocab_size = 128256
rope_theta = 500000
```

## 2. Tokenize

用 [[llamaTokenizer]] 将文本编码为 token ids，并添加 `<|begin_of_text|>`。

示例 token 数：17。

```text
[128000, 1820, 4320, ...]
```

## 3. Embedding 查表

```python
embedding_layer = torch.nn.Embedding(vocab_size, dim)
embedding_layer.weight.data.copy_(model["tok_embeddings.weight"])
token_embeddings = embedding_layer(tokens)
```

形状：

```text
[seq_len, dim] = [17, 4096]
```

## 4. RMSNorm

进入 attention 前做 RMSNorm：

```python
x_norm = rms_norm(x, model["layers.0.attention_norm.weight"])
```

公式：

```text
RMSNorm(x) = x / sqrt(mean(x^2) + eps) * gamma
```

## 5. Q/K/V 投影

第 0 层权重：

```text
Wq: [4096, 4096]
Wk: [1024, 4096]
Wv: [1024, 4096]
Wo: [4096, 4096]
```

其中 K/V 只有 1024 维，是因为 Llama 3 8B 使用 [[transformer/inferenceOptimization|GQA]]：

```text
8 KV heads × 128 head_dim = 1024
```

## 6. 拆 head

Q 权重拆成：

```text
[32, 128, 4096]
```

K/V 权重拆成：

```text
[8, 128, 4096]
```

每个 Q head 输出：

```text
[17, 128]
```

## 7. RoPE

对 Q/K 应用 RoPE：

1. 把 128 维 head 向量拆成 64 个二维 pair。
2. 构造不同频率的复数旋转因子。
3. 按 token 位置旋转 Q/K。
4. 转回实数向量。

核心形式：

```text
z' = z · e^{j m θ}
```

其中 `m` 是 token 位置。

## 8. Causal Attention

计算 attention score：

```text
scores = QK^T / sqrt(128)
```

加上 causal mask：

```text
未来位置 = -inf
```

softmax：

```text
weights = softmax(scores + mask)
```

聚合 V：

```text
head_output = weights @ V
```

单个 head 输出：

```text
[17, 128]
```

## 9. GQA 映射

Llama 3 8B：

```text
32 Q heads, 8 KV heads
```

每 4 个 Q heads 共享一个 KV head：

```python
k_head = k_layer[head // 4]
v_head = v_layer[head // 4]
```

## 10. 拼接多头并输出投影

32 个 head 输出拼接：

```text
[17, 4096]
```

再乘 `Wo`：

```text
attention_delta = concat_heads @ Wo.T
```

残差连接：

```text
x = x + attention_delta
```

## 11. SwiGLU FFN

attention 后做 RMSNorm，再进入 SwiGLU：

```python
output_after_feedforward = (
    silu(x_norm @ w1.T) * (x_norm @ w3.T)
) @ w2.T
```

公式：

```text
FFN(x) = W2(SiLU(xW1) ⊙ xW3)
```

残差：

```text
x = x + ffn_output
```

## 12. 堆叠 32 层

对 32 层重复：

```text
x = x + Attention(RMSNorm(x))
x = x + SwiGLU(RMSNorm(x))
```

## 13. 最终 logits

最后做 final RMSNorm：

```python
final_embedding = rms_norm(final_embedding, model["norm.weight"])
```

取最后一个 token：

```python
h_last = final_embedding[-1]
```

投影到词表：

```python
logits = h_last @ model["output.weight"].T
```

形状：

```text
[128256]
```

然后：

```python
next_token = argmax(logits)
```

## 张量形状速查

| 对象 | 形状 |
|---|---|
| token embeddings | `[17, 4096]` |
| 单个 Q head | `[17, 128]` |
| Q 权重拆分 | `[32, 128, 4096]` |
| K/V 权重拆分 | `[8, 128, 4096]` |
| attention score | `[17, 17]` |
| 单 head 输出 | `[17, 128]` |
| 多头 concat | `[17, 4096]` |
| output weight | `[128256, 4096]` |
| logits | `[128256]` |

## 面试重点

1. Llama 3 推理中每层先 RMSNorm，再 Attention/FFN。
2. RoPE 只作用在 Q/K，不作用在 V。
3. GQA 中多个 Q heads 共享 K/V。
4. SwiGLU FFN 使用 `w1/w2/w3` 三组权重。
5. 最终只取最后一个 token 的 hidden state 预测下一个 token。
6. 真实部署会用 [[transformer/inferenceOptimization|transformerKvCache]]，避免每步重复计算历史 token。

## 关联连接

- [[llamaOverview]] — Llama 3 总览
- [[llamaArchitecture]] — Llama 3 架构
- [[llamaTokenizer]] — tokenizer 实现
- [[transformer/inferenceOptimization|transformerGqaRope]] — GQA 与 RoPE
- [[transformer/inferenceOptimization|transformerKvCache]] — KV Cache
- [[transformer/inferenceOptimization|transformerDecodingSampling]] — logits 到 token 的选择策略

## 原始资料

- `raw/大模型学习/第2课-llama3-from-scratch/llama3-from-scratch-Copy1.ipynb`
- `raw/大模型学习/第2课-llama3-from-scratch/images/`
