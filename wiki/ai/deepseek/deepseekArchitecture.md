---
type: knowledge
tags: [AI, DeepSeek, MLA, MoE, 架构]
article_id: OBA-deepseek-architecture
created_at: 2026/06/23
updated_at: 2026/06/23
---

# DeepSeek 架构：MLA 与 MoE

[[deepseekArchitecture]] 总结 DeepSeek-V2/V3 的核心架构：MLA 负责降低 attention 侧 KV Cache 成本，DeepSeekMoE 负责扩大 FFN 侧参数容量。二者共同构成 DeepSeek 高性价比大模型的基础。

## 架构总览

DeepSeek 的核心架构组合：

```text
Transformer backbone
+ MLA attention
+ DeepSeekMoE FFN
+ 长上下文训练/推理
+ FP8 / MTP 等工程优化
```

其中：

- **MLA**：Multi-Head Latent Attention，压缩 KV Cache。
- **DeepSeekMoE**：稀疏专家网络，扩大总参数量但控制激活计算。

## MLA：Multi-Head Latent Attention

传统 attention 推理时需要缓存每层、每个 token 的 K/V。上下文越长，KV Cache 越大。

MLA 的思想是：

```text
不直接缓存完整 K/V
→ 先压缩成低维 latent
→ 推理时缓存 latent
→ 用时恢复或参与 attention 计算
```

与 MHA/GQA/MQA 的关系：

| 机制 | 降低 KV Cache 的方式 |
|---|---|
| MHA | 不降低，每个 head 独立 K/V |
| MQA | 所有 Q heads 共享一组 K/V |
| GQA | 多个 Q heads 分组共享 K/V |
| MLA | 将 K/V 压缩为低维 latent 表示 |

MLA 更像从表示维度压缩 KV Cache，而不只是减少 KV heads 数量。

## MLA 的价值

- 降低长上下文推理显存。
- 降低显存带宽压力。
- 支撑 128K 等长上下文场景。
- 与 MoE 结合后，既降低 attention 成本，又扩展 FFN 参数容量。

## DeepSeekMoE

MoE 的基本目标：

```text
总参数量很大，但每个 token 只激活少量专家
```

DeepSeek-V2：

- 约 236B 总参数。
- 每 token 约 21B 激活参数。
- 使用 DeepSeekMoE + MLA。

DeepSeek-V3 / R1：

- 约 671B 总参数。
- 每 token 约 37B 激活参数。
- 更大规模 MoE。
- 延续 MLA。

## MoE 的关键问题

MoE 的难点包括：

- 路由器如何选择专家。
- 专家负载是否均衡。
- 专家并行的 all-to-all 通信成本。
- 部分专家过载或冷启动。
- 辅助负载均衡损失可能干扰主任务。

DeepSeek-V3 资料中强调无辅助损失负载均衡，目标是减少传统 auxiliary loss 对主任务优化的干扰。

## V3 的工程优化

DeepSeek-V3 的关键词：

- **671B 总参数 / 约 37B 激活**：大容量稀疏模型。
- **MLA**：降低 KV Cache。
- **FP8**：低精度训练降低成本。
- **MTP**：Multi-Token Prediction，同时预测多个后续 token，提高训练效率与泛化。
- **无辅助损失负载均衡**：改进 MoE 训练稳定性与主任务性能。

## 与 Llama 架构的对比

| 维度 | [[llama/llamaArchitecture|Llama 3]] | DeepSeek-V3/R1 |
|---|---|---|
| 主体 | Decoder-only Transformer | Decoder-only Transformer 变体 |
| Attention 优化 | GQA | MLA |
| FFN | SwiGLU dense FFN | DeepSeekMoE |
| 参数激活 | 稠密激活 | 稀疏专家激活 |
| 推理重点 | GQA + KV Cache | MLA + MoE 并行 |
| 后训练 | instruct/chat | R1 强化推理训练 |

## 面试要点

### DeepSeek 为什么低成本？

答：MLA 降低 KV Cache，MoE 降低每 token 激活计算，FP8 降低训练成本，GRPO 降低推理 RL 成本。

### MLA 和 GQA 的区别？

答：GQA 是减少 K/V heads 数量；MLA 是把 K/V 压缩到低维 latent 表示。二者都服务于降低 KV Cache，但压缩路径不同。

### MoE 的收益和难点？

答：收益是总参数量大、激活计算小；难点是路由、负载均衡、通信和训练稳定性。

## 关联连接

- [[deepseekOverview]] — DeepSeek 系列总览
- [[deepseekR1]] — R1 强化学习推理模型
- [[MoE]] — 混合专家模型
- [[transformer/inferenceOptimization|transformerAttentionOptimization]] — MLA 与注意力优化
- [[transformer/modernArchitectures|transformerMoEArchitecture]] — Transformer MoE 通用知识
- [[transformer/inferenceOptimization|transformerKvCache]] — KV Cache
- [[llama/llamaArchitecture]] — Llama 3 架构对照

## 原始资料

- `raw/大模型学习/第2课-llama3-from-scratch/【拓展篇】万字详解Deepseek系列.pdf`
