---
type: knowledge
tags: [AI, Transformer, 计算过程, Attention, 手写实现]
article_id: OBA-transformer-computation-process
created_at: 2026/06/24
updated_at: 2026/06/24
---

# Transformer 计算过程

[[transformerComputationProcess]] 基于 `手搓transformer.ipynb`、`手撕Attention.ipynb`，并结合 AI by Hand 的 `Self-Attention.xlsx`、`Multihead-Attention.xlsx`、`Transformer.xlsx`、`Transformer-Full-Stack.xlsx`，从“矩阵如何一步步流动”的角度总结 [[Transformer]] 的前向计算。

## 总体计算链路

一个教学版 Transformer 前向传播可以抽象为：

```text
原始文本
→ BPE / Tokenizer
→ token ids
→ Embedding lookup
→ 乘 sqrt(d_model)
→ 加 Positional Encoding
→ 多层 Transformer Block
   → Multi-Head Self-Attention
   → Residual + LayerNorm
   → FFN
   → Residual + LayerNorm
→ Linear 投影到 vocab_size
→ Softmax 得到下一个 token 概率
```

`手搓transformer.ipynb` 中的具体运行示例是：

```text
输入文本: 我爱学习大语言模型
Token 数: 9
batch_size = 1
d_model = 64
num_heads = 4
head_dim = 16
num_layers = 2
d_ff = 128
vocab_size = 20
```

因此关键张量形状流动为：

```text
input_ids: [1, 9]
embedding: [1, 9, 64]
positional encoded: [1, 9, 64]
attention output: [1, 9, 64]
ffn output: [1, 9, 64]
logits: [1, 9, 20]
probabilities: [1, 9, 20]
```

## 1. Tokenization：文本变成整数

`手搓transformer.ipynb` 实现了一个简化版 `SimpleBPE`：

1. 从语料中收集字符级初始词表。
2. 统计相邻符号 pair 的频率。
3. 反复合并最高频 pair，直到达到目标词表大小。
4. 将输入文本编码为 token id。

示例输出：

```text
Tokenized IDs: [15, 17, 14, 11, 13, 19, 18, 16, 12]
对应 Tokens: ['我', '爱', '学', '习', '大', '语', '言', '模', '型']
```

这里的重点不是 BPE 工业实现，而是建立第一层直觉：

> 模型不直接处理文字，只处理 token id；后续所有计算都发生在向量和矩阵上。

详见 [[foundations]]。

## 2. Embedding：整数查表变成向量

Embedding 层本质是查表：

```text
input_ids: [B, T]
embedding_table: [vocab_size, d_model]
output: [B, T, d_model]
```

notebook 中实现：

```python
output = self.embedding(x) * math.sqrt(self.d_model)
```

为什么乘 `sqrt(d_model)`：原始 Transformer 中会对 token embedding 做缩放，使 embedding 的数值尺度与位置编码、后续 attention 计算更匹配。

在示例中：

```text
[1, 9] → [1, 9, 64]
```

AI by Hand 的 `Transformer-Full-Stack.xlsx` 也展示了相同思想：左侧输入 token 经由 one-hot / embedding table 变为一组连续向量，后续所有模块都只处理这些向量。

## 3. Positional Encoding：注入顺序信息

Self-Attention 本身不知道 token 顺序，因此 `手搓transformer.ipynb` 使用 sin/cos 位置编码：

```text
PE(pos, 2i)   = sin(pos / 10000^(2i / d_model))
PE(pos, 2i+1) = cos(pos / 10000^(2i / d_model))
```

计算方式：

```text
x = token_embedding + positional_encoding
```

形状不变：

```text
[1, 9, 64] → [1, 9, 64]
```

`Transformer-Full-Stack.xlsx` 中可以看到 “Positional” 区域，表示位置向量被加到 token 表示上。

## 4. Multi-Head Attention：核心矩阵流动

`手撕Attention.ipynb` 用输入：

```text
X: [16, 64, 512]
B = 16
T = 64
D = 512
num_heads = 8
head_dim = 64
```

`手搓transformer.ipynb` 的小模型则是：

```text
X: [1, 9, 64]
num_heads = 4
head_dim = 16
```

两者计算逻辑相同。

### 4.1 线性投影得到 Q/K/V

对输入分别做三组线性变换：

```text
Q = X Wq
K = X Wk
V = X Wv
```

形状保持为：

```text
[B, T, D] → [B, T, D]
```

Excel 的 `Self-Attention.xlsx` 和 `Transformer.xlsx` 中可以看到 `Wq`、`Wk`、`Wv` 以及 `MMULT(...)` 公式，这对应矩阵乘法投影。

## 4.2 拆分多头

将 `D` 拆成 `num_heads × head_dim`：

```text
[B, T, D]
→ view(B, T, H, head_dim)
→ transpose(1, 2)
→ [B, H, T, head_dim]
```

例如 `手撕Attention.ipynb`：

```text
[16, 64, 512]
→ [16, 64, 8, 64]
→ [16, 8, 64, 64]
```

这样每个 head 可以在自己的子空间中独立计算 attention。

## 4.3 计算注意力分数

每个 head 中：

```text
scores = Q @ K^T / sqrt(head_dim)
```

形状：

```text
Q:      [B, H, T, head_dim]
K^T:    [B, H, head_dim, T]
scores: [B, H, T, T]
```

`Self-Attention.xlsx` 中明确展示：

```text
K^T * Q / sqrt(dk)
```

`Multihead-Attention.xlsx` 中也出现公式：

```text
MMULT(..., ...) / SQRT(3)
```

这说明 Excel 手算材料是在逐格展开同一件事：每个 query token 与每个 key token 做点积，再除以 `sqrt(d_k)`。

## 4.4 Causal Mask：屏蔽未来

`手撕Attention.ipynb` 使用下三角 mask：

```python
mask = torch.tril(torch.ones(T, T, dtype=bool))
score = score.masked_fill(mask == 0, -10000)
```

含义：第 t 个 token 只能看见自己和之前的 token，不能偷看未来。

在 `Transformer-Full-Stack.xlsx` 中也能看到 “Casual Mask”（应为 Causal Mask）以及：

```text
K^T * Q / sqrt(dk) + causal mask
```

注意：`手搓transformer.ipynb` 的 `MultiHeadAttention` 接收 `mask=None`，但示例 `EncoderLayer` 没有实际传入 causal mask，所以它更像教学版 Encoder self-attention；`手撕Attention.ipynb` 则演示了 decoder-style causal attention。

## 4.5 Softmax：分数变权重

对最后一维做 softmax：

```text
attention_weights = softmax(scores, dim=-1)
```

形状仍是：

```text
[B, H, T, T]
```

含义：对每个 query token，它对所有 key token 的注意力权重之和为 1。

在 `Self-Attention.xlsx`、`Multihead-Attention.xlsx` 中可以看到 `SOFTMAX(...)` 公式，说明 Excel 手算正是把分数行归一化成概率分布。

## 4.6 加权求和 V

```text
context = attention_weights @ V
```

形状：

```text
attention_weights: [B, H, T, T]
V:                 [B, H, T, head_dim]
context:           [B, H, T, head_dim]
```

含义：每个 token 根据注意力权重，从所有可见 token 的 value 中混合出自己的新表示。

## 4.7 合并多头并输出投影

先把多头转回 token 维度：

```text
[B, H, T, head_dim]
→ transpose(1, 2)
→ [B, T, H, head_dim]
→ contiguous().view(B, T, D)
→ [B, T, D]
```

然后经过输出线性层：

```text
output = Wo(context)
```

形状仍是：

```text
[B, T, D]
```

`contiguous()` 的作用是：`transpose` 后内存布局可能不连续，必须先整理内存才能安全 `view`。

## 5. Residual + LayerNorm：稳定深层网络

`手搓transformer.ipynb` 中 EncoderLayer 的结构是：

```python
attn_output = self.self_attn(x, x, x)
x = self.norm1(x + self.dropout(attn_output))

ffn_output = self.ffn(x)
x = self.norm2(x + self.dropout(ffn_output))
```

即：

```text
x = LayerNorm(x + Attention(x))
x = LayerNorm(x + FFN(x))
```

`手撕Attention.ipynb` 也实现了 LayerNorm：

```text
mean = x.mean(-1)
var = x.var(-1)
out = (x - mean) / sqrt(var + eps) * gamma + beta
```

LayerNorm 对每个 token 的 hidden dimension 做归一化，形状不变：

```text
[B, T, D] → [B, T, D]
```

## 6. FFN：逐位置非线性变换

`手搓transformer.ipynb` 的 FFN：

```python
output = linear2(ReLU(linear1(x)))
```

形状变化：

```text
[B, T, d_model]
→ Linear1: [B, T, d_ff]
→ ReLU
→ Linear2: [B, T, d_model]
```

示例参数：

```text
d_model = 64
d_ff = 128
```

所以：

```text
[1, 9, 64] → [1, 9, 128] → [1, 9, 64]
```

FFN 不在 token 之间交互；token 间交互已经由 Attention 完成。FFN 负责对每个位置的表示做非线性加工。

## 7. 堆叠多层 EncoderLayer

`手搓transformer.ipynb` 中：

```python
self.layers = nn.ModuleList([
  EncoderLayer(d_model, num_heads, d_ff)
  for _ in range(num_layers)
])
```

示例 `num_layers=2`，所以计算链路是：

```text
Embedding + PE
→ EncoderLayer 1
→ EncoderLayer 2
→ Linear to vocab
```

每层输入输出形状都保持：

```text
[1, 9, 64]
```

这就是 Transformer 能够堆叠变深的原因：每个 block 都保持相同 hidden size。

## 8. Linear + Softmax：得到词表概率

最后一层线性投影：

```text
logits = Linear(hidden)
```

形状：

```text
[B, T, d_model] → [B, T, vocab_size]
```

示例中：

```text
[1, 9, 64] → [1, 9, 20]
```

然后：

```text
probs = softmax(logits, dim=-1)
```

得到每个位置对词表中每个 token 的概率分布。生成任务通常取最后一个位置：

```text
probs[:, -1, :] → next token distribution
```

`Transformer-Full-Stack.xlsx` 中的 “Prediction (Y)” 和 “Output Probabilities” 区域对应这一步。

## 9. Excel 文件如何对应代码

| Excel 文件 | 对应代码模块 | 重点 |
|---|---|---|
| `Self-Attention.xlsx` | 单头 attention | Q/K/V、`QK^T/sqrt(dk)`、softmax、加权 V |
| `Multihead-Attention.xlsx` | MultiHeadAttention | 多个 head 并行计算、concat、输出投影 |
| `Transformer.xlsx` | Transformer block | Attention、FFN、残差、归一化的局部结构 |
| `Transformer-Full-Stack.xlsx` | 完整前向链路 | token、position、attention、mask、normalize、prediction |

Excel 的价值在于：它把 PyTorch 中一行矩阵乘法拆成了可见的单元格公式。例如：

- `MMULT(...)` 对应线性层或点积矩阵乘法。
- `/ SQRT(dk)` 对应 scaled attention。
- `SOFTMAX(...)` 对应注意力权重归一化。
- `TRANSPOSE(...)` 对应 K 转置或矩阵布局调整。
- “Causal Mask” 对应 decoder 自回归约束。

## 10. 计算过程速记

```text
1. 文本 → token ids
2. token ids → embedding: [B,T] → [B,T,D]
3. 加位置编码: [B,T,D]
4. Q/K/V 投影: [B,T,D] → 3 × [B,T,D]
5. 拆头: [B,T,D] → [B,H,T,d]
6. 注意力分数: Q @ K^T / sqrt(d) → [B,H,T,T]
7. mask: 未来位置填 -∞
8. softmax: 分数 → 权重
9. 加权 V: weights @ V → [B,H,T,d]
10. 合并头: [B,H,T,d] → [B,T,D]
11. 输出投影: [B,T,D]
12. 残差 + LayerNorm
13. FFN: [B,T,D] → [B,T,d_ff] → [B,T,D]
14. 残差 + LayerNorm
15. 重复 N 层
16. Linear 到词表: [B,T,D] → [B,T,V]
17. Softmax 得到 token 概率
```

## 11. 教学实现与真实 LLM 的差异

这些 notebook 和 Excel 主要用于理解计算过程，和真实 LLM 仍有差异：

- `手搓transformer.ipynb` 更像 Encoder-only 教学实现，没有完整 decoder block、KV Cache、RoPE、GQA。
- `手撕Attention.ipynb` 演示了 causal mask，更贴近 decoder self-attention。
- 现代 LLM 通常使用 RMSNorm、RoPE、GQA/MLA、SwiGLU、KV Cache、Flash Attention。
- 工业实现会使用高性能 kernel，不会逐单元格或逐 Python 小矩阵计算。

理解这些简化版计算后，再看 [[inferenceOptimization]] 中的 KV Cache、GQA、RoPE、Flash Attention 会更自然。

## 原始资料

- `raw/大模型学习/第1课-Transformer/手搓transformer.ipynb`
- `raw/大模型学习/第1课-Transformer/手撕Attention.ipynb`
- `raw/大模型学习/第1课-Transformer/ai-by-hand-excel/advanced/Self-Attention.xlsx`
- `raw/大模型学习/第1课-Transformer/ai-by-hand-excel/advanced/Multihead-Attention.xlsx`
- `raw/大模型学习/第1课-Transformer/ai-by-hand-excel/advanced/Transformer.xlsx`
- `raw/大模型学习/第1课-Transformer/ai-by-hand-excel/advanced/Transformer-Full-Stack.xlsx`

## 关联连接

- [[Transformer]] — Transformer 总览
- [[foundations]] — Tokenization、Embedding、Attention 基础
- [[inferenceOptimization]] — KV Cache、GQA、RoPE、Flash Attention 与解码采样
- [[modernArchitectures]] — 现代 Decoder-only LLM 架构
- [[practice]] — Transformer 代码实践与 Excel 手算
- [[LLMInference]] — 推理优化与部署
