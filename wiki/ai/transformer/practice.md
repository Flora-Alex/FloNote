---
type: knowledge
tags: [AI, Transformer, 代码实践, Excel手算]
article_id: OBA-transformer-practice
created_at: 2026/06/23
updated_at: 2026/06/24
---

# Transformer 实践：手写代码与 Excel 手算

[[practice]] 汇总 Transformer 代码实践与 Excel 手算材料，帮助把公式、矩阵形状和模块实现串起来。

## 学习路径

```text
Dot Product
→ Matrix Multiplication
→ Linear Layer
→ Softmax
→ Self-Attention
→ Multi-Head Attention
→ Transformer Block
→ PyTorch 实现
→ KV Cache / GQA / RoPE
```

如果要按“张量形状如何一步步变化”来理解完整前向传播，优先阅读 [[transformerComputationProcess]]。

## PyTorch 手写 Attention

核心输入形状：

```text
x: [batch, seq_len, d_model]
```

实现步骤：

1. 定义 Q/K/V/O 线性层。
2. 计算 Q/K/V。
3. 拆分多头：`[B, T, D] → [B, num_heads, T, head_dim]`。
4. 计算注意力分数：`scores = q @ k.T / sqrt(head_dim)`。
5. 加 causal mask 或 padding mask。
6. softmax 得到权重。
7. `weights @ v` 得到上下文。
8. 合并多头。
9. output projection。

## 为什么需要 contiguous

PyTorch 中 `transpose` 后张量可能不连续，直接 `view` 可能报错或得到错误视图。常见写法：

```python
x = x.transpose(1, 2).contiguous().view(batch, seq_len, d_model)
```

## 手写 LayerNorm

LayerNorm 对每个 token 的 hidden dimension 做归一化：

```text
mean = x.mean(dim=-1)
var = x.var(dim=-1)
y = (x - mean) / sqrt(var + eps) * gamma + beta
```

作用：稳定训练、改善梯度传播。

## 简化 Transformer

课程 notebook 中的简化 Transformer 包含：

- SimpleBPE。
- Embedding。
- PositionalEncoding。
- MultiHeadAttention。
- FFN。
- EncoderLayer。
- Transformer 主体。
- logits / softmax。

注意：该实现更偏教学版或 Encoder-only，不等于完整工业级 Decoder-only LLM。

## Excel 手算材料

Excel 材料用于把矩阵计算完全展开：

### 基础数学模块

- Dot Product。
- Matrix Multiplication。
- Linear Layer。
- Softmax。
- Temperature。

### Self-Attention.xlsx

演示 Q/K/V、attention score、softmax 权重、weighted sum。

### Multihead-Attention.xlsx

演示多个 head 如何分别计算，再 concat 和 output projection。

### Transformer.xlsx / Transformer-Full-Stack.xlsx

演示 embedding、position encoding、attention、FFN、residual、norm、logits 的完整链路。

## 代码实践的边界

- 教学代码通常省略高性能 kernel、KV Cache、GQA、RoPE、Flash Attention。
- 小模型实现有助于理解形状和公式，但不能代表生产 LLM 的全部工程复杂度。
- 真正部署还需要量化、并行、缓存、调度和服务化。

## 原始资料

- `raw/大模型学习/第1课-Transformer/动手学大模型/`
- `raw/大模型学习/第1课-Transformer/【小白也能懂】手搓Transformer/`
- `raw/大模型学习/第1课-Transformer/动手学大模型/手撕attention.ipynb`
- `raw/大模型学习/第1课-Transformer/动手学大模型/构建完整Transformer.ipynb`
- `wiki/ai/transformer/transformerCodePractice.md` 旧版内容
- `wiki/ai/transformer/transformerByHandExcel.md` 旧版内容

## 关联连接

- [[Transformer]] — 总览与架构
- [[transformerComputationProcess]] — Transformer 前向计算过程
- [[foundations]] — Q/K/V、Attention、Embedding 基础
- [[inferenceOptimization]] — 推理优化
- [[modernArchitectures]] — 现代 LLM 架构
- [[LLMTraining]] — 训练流程
