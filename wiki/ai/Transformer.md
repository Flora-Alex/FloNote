---
type: knowledge
tags:
  - AI
  - Transformer
  - 深度学习
  - 注意力机制
article_id: OBA-ai-transformer
created_at: 2026/05/15
updated_at: 2026/05/15
---

# Transformer

Transformer 是现代 [[LLM]] 的基础架构，由 Vaswani 等人在 2017 年论文 *Attention Is All You Need* 中提出。它彻底抛弃了循环结构，完全基于注意力机制实现序列建模，成为自然语言处理乃至多模态领域的核心范式。

## Transformer 架构

### RNN 的两个致命缺陷

在 Transformer 出现之前，序列建模主要依赖 RNN/LSTM/GRU，但它们存在两个根本性问题：

1. **顺序计算无法并行**：第 $t$ 步的计算依赖第 $t-1$ 步的隐藏状态，无法利用 GPU 的并行计算能力，训练效率极低
2. **长距离梯度消失**：即使 LSTM 引入了门控机制，信息经过数十到上百步传递后仍然会严重衰减，难以捕捉长距离依赖

### Self-Attention 核心机制

Self-Attention 是 Transformer 的核心，它让序列中的每个位置都能直接"看到"所有其他位置，一步到位建立全局依赖。

**Q/K/V 三个线性投影**：输入序列 $X$ 分别经过三个线性变换，得到查询（Query）、键（Key）、值（Value）三个矩阵：

$$Q = XW_Q, \quad K = XW_K, \quad V = XW_V$$

**注意力计算公式**：

$$\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right) V$$

- $QK^T$ 计算每对位置之间的相关性分数
- 除以 $\sqrt{d_k}$ 是关键细节（见下文）
- softmax 将分数归一化为概率分布
- 用该分布对 $V$ 加权求和，得到每个位置的上下文感知表示

### 为什么要除以 $\sqrt{d_k}$

当 $d_k$ 较大时，$Q$ 和 $K$ 的点积结果方差会随之增大，导致 softmax 输入的绝对值偏大。softmax 在输入值差异很大时，输出会趋近于 one-hot 分布（接近 1 和接近 0），这会导致：

- 梯度极其微小，接近梯度消失
- 模型几乎只关注单个位置，丧失了"软注意力"的优势

除以 $\sqrt{d_k}$ 将方差重新归一化到 1，使 softmax 的输出保持合理的概率分布，梯度也能稳定传播。

### Multi-Head Attention

单头注意力只能捕捉一种关联模式。Multi-Head Attention 将 Q/K/V 投影到 $h$ 个不同的子空间，每组独立计算注意力后再拼接：

$$\text{MultiHead}(Q, K, V) = \text{Concat}(\text{head}_1, \ldots, \text{head}_h) W_O$$

其中每个 head $i$ 使用不同的投影矩阵：

$$\text{head}_i = \text{Attention}(QW_i^Q, KW_i^K, VW_i^V)$$

多组头可以同时捕捉不同类型的语言关联——例如有的头关注语法结构，有的关注语义相似性，有的关注共指关系。

### FFN（前馈网络）

每个 Transformer 层在注意力之后还有一个两层全连接网络：

$$\text{FFN}(x) = \text{GELU}(xW_1 + b_1)W_2 + b_2$$

FFN 的特点：

- 对每个位置**独立**做非线性变换（位置之间不交互）
- 中间层维度通常是隐藏维度的 4 倍（如 $d_{model}=4096$，则中间层为 $16384$）
- 是模型的"记忆仓库"——研究表明 Transformer 的大部分知识存储在 FFN 的权重中

### 位置编码

Self-Attention 本身是**位置盲**的——它把输入当作一个集合而非序列，打乱输入顺序会得到相同的注意力分布（只是输出顺序跟着打乱）。因此需要显式注入位置信息，让模型知道每个 token 在序列中的位置。

详见下文 [位置编码](#位置编码) 章节。

## 三种架构变体

原始 Transformer 由 Encoder 和 Decoder 两部分组成。后续发展出了三种主要变体：

| 架构 | 代表模型 | 注意力方式 | 预训练目标 | 擅长任务 |
|---|---|---|---|---|
| Encoder-only | BERT | 双向注意力 | MLM（掩码语言建模） | 理解任务（分类、NER、问答） |
| Decoder-only | GPT / Claude / Qwen | 单向（因果掩码） | CLM（因果语言建模） | 文本生成 |
| Encoder-Decoder | T5 / BART | Encoder 双向 + Decoder 单向 | 复合目标 | 翻译、摘要 |

### 为什么 Decoder-only 赢了

当前主流 [[LLM]]（GPT-4、Claude、Qwen、DeepSeek 等）几乎全部采用 Decoder-only 架构，原因在于：

1. **统一目标**：预测下一个 token（CLM）是一个简洁的自监督目标，不需要像 MLM 那样设计特殊的掩码策略
2. **任务通用性**：所有 NLP 任务都可以表达为"续写"——问答是续写答案，翻译是续写目标语言，推理是续写推理链
3. **规模效应**：Decoder-only 架构在扩大规模时涌现能力（Emergent Abilities）最强，性能随参数量、数据量、计算量的幂律增长最为稳定
4. **训练效率**：因果掩码使每一步只需关注前文，推理时天然支持自回归生成

## 多头注意力优化（MQA / GQA / Flash Attention）

### MHA 的三个痛点

标准 Multi-Head Attention（MHA）在实际部署中面临严重瓶颈：

1. **显存爆炸**：自回归生成时需要缓存已计算的 K/V（KV Cache），显存占用巨大
2. **访存慢（memory-bound）**：注意力计算主要是矩阵乘法，但受限于显存带宽，计算单元利用率低
3. **$O(N^2)$ 复杂度**：注意力矩阵大小与序列长度平方成正比，长上下文时代价极高

### KV Cache 显存公式

$$\text{KV Cache} = 2 \times B \times N \times L \times H \times d_k \times 2 \text{ bytes (FP16)}$$

- $B$：batch size
- $N$：层数
- $L$：序列长度
- $H$：注意力头数
- $d_k$：每个头的维度

例如一个 70B 模型（80 层、64 头、128 维），序列长度 4096，batch size 1，KV Cache 约需 5GB 显存。

### MQA（Multi-Query Attention）

**核心思想**：所有 head 共享同一份 K 和 V，只有 Q 保留独立投影。

- KV Cache 降到原来的 $1/H$
- 效果损失约 2-5%
- 代表：PaLM、StarCoder

### GQA（Grouped-Query Attention）

**核心思想**：$H$ 个 head 分成 $G$ 组，每组内的 head 共享同一份 K/V。

- 当 $G=H$ 时退化为 MHA，当 $G=1$ 时退化为 MQA
- $G=8$ 时效果接近 MHA（损失 < 0.5%），KV Cache 压到 $1/4$
- 代表：Llama 2/3、Mistral、Qwen

### Flash Attention

**核心思想**：分块计算 + 在线 softmax，在 GPU SRAM（片上高速缓存）中完成注意力计算，避免反复读写 HBM（显存）。

- 显存从 $O(N^2)$ 降到 $O(N)$
- 速度提升 2-4 倍
- **数学完全等价**，精度无损
- 代表：已被几乎所有主流框架和模型采用

### 三类优化的关系

这三类优化是**叠加关系**，不是替代关系。当前主流标配为：

> **GQA + Flash Attention**

### 拓展方向

- **MLA（Multi-head Latent Attention）**：DeepSeek V2/V3 提出，通过低秩压缩进一步降低 KV Cache，效果接近 MHA 但显存占用极低
- **Sliding Window Attention**：Mistral 采用，每个位置只关注固定窗口内的 token，将复杂度从 $O(N^2)$ 降到 $O(N \cdot w)$
- **Mamba / SSM**：状态空间模型，用线性递归替代注意力，$O(N)$ 复杂度且无需 KV Cache，是 Transformer 的有力竞争者

## 位置编码

### sin/cos 绝对位置编码

原始 Transformer 使用正弦/余弦函数生成位置编码：

$$PE_{(pos, 2i)} = \sin(pos / 10000^{2i/d_{model}})$$
$$PE_{(pos, 2i+1)} = \cos(pos / 10000^{2i/d_{model}})$$

- 优点：零参数，无需学习
- 缺点：长上下文外推能力差，训练时没见过的序列长度效果骤降

### RoPE（旋转位置编码）

**核心思想**：将位置信息编码为 Q/K 向量的旋转角度，使点积结果天然包含相对位置信息。

- 天然编码**相对位置**关系
- 长上下文外推能力强
- 零参数
- 兼容 KV Cache、Flash Attention、MQA、GQA
- **采用者**：Llama、Qwen、DeepSeek、Mistral、GLM 等几乎所有主流开源模型

RoPE 已成为事实标准。

### ALiBi

**核心思想**：在注意力分数上直接加一个距离惩罚项 $-m \cdot |i - j|$。

- 零参数，实现极简
- 外推能力不错
- 表达力相对较弱，已被 RoPE 边缘化

### 长上下文外推技巧

当模型需要处理比训练时更长的序列时，可采用以下技术：

- **PI（Position Interpolation）**：将位置编码的范围线性缩放到目标长度，相当于"压缩"位置空间
- **NTK-aware Scaling**：调整 RoPE 的频率基数，使高频分量衰减更慢，保留更多局部细节
- **YaRN**：结合 NTK 缩放和注意力温度调节，是目前效果最好的外推方案之一

## 分词器（Tokenizer）

### 为什么需要分词器

模型只能处理整数（token ID），不能直接处理文字。Tokenizer 是连接人类文字世界和模型整数世界的桥梁，负责将文本切分为离散单元并映射为整数 ID。

### BPE 算法

Byte Pair Encoding 是当前最主流的分词算法，分三步：

1. **初始化**：将文本拆成最小单元（字节或字符）
2. **反复合并**：统计所有相邻 pair 的频率，合并最高频的 pair 为一个新 token
3. **终止**：词汇表达到预设大小时停止

BPE 的优势是能自动学习常见的子词组合（如 "un"、"ing"、"tion"），平衡了词汇表大小和序列长度。

### 中文分词

- 常用汉字通常直接作为独立 token 存在于词汇表中
- 1000 个汉字大约对应 1000-1500 个 tokens
- 生僻字可能被拆分为多个字节 token，效率较低

### 特殊 Token

模型词汇表中保留了一些具有特殊功能的 token：

- **BOS**（Beginning of Sequence）：标记序列开始
- **EOS**（End of Sequence）：标记序列结束
- **PAD**（Padding）：对齐不同长度的序列
- **SEP**（Separator）：分隔不同段落或句子
- **ChatML 格式**：用特殊 token 标记对话角色（如 `<|im_start|>user`、`<|im_start|>assistant`），已成为多轮对话的事实标准

### 工程影响

- **API 成本**：大多数 LLM 服务按 token 数计费，分词粒度直接影响费用
- **上下文窗口**：模型的上下文长度限制以 token 为单位（如 8K、32K、128K），而非字符数
- **分词器选择**：不同模型的分词器不同，同一个文本在不同模型中 token 数可能差异显著

## 相关链接

- [[LLM]]：基于 Transformer Decoder-only 架构的大规模语言模型
- [[MoE]]：混合专家架构，在 Transformer FFN 层引入稀疏路由
- [[LLMTraining]]：Transformer 模型的训练流程与技巧
