---
type: summary
tags: [AI, DeepSeek, 大模型, MoE, 推理模型]
article_id: OBA-deepseek-overview
created_at: 2026/06/23
updated_at: 2026/06/23
---

# DeepSeek 系列总览

[[deepseekOverview]] 总结 `【拓展篇】万字详解Deepseek系列.pdf` 与 `【论文精读-3】Deepseek-R1论文精读.pdf` 的核心脉络。DeepSeek 系列的主线是：用 [[MoE]] 扩容量，用 MLA 降低 KV Cache 成本，用 V3 构建强基座，再用 GRPO 和多阶段 RL 激发推理能力。

## 一句话主线

```text
DeepSeek-LLM
→ DeepSeekMoE
→ DeepSeek-V2：MLA + MoE
→ DeepSeek-V3：更大 MoE + MLA + FP8 + MTP
→ DeepSeek-R1：基于 V3 的强化学习推理模型
```

## 时间线

| 时间 | 模型 | 核心意义 |
|---|---|---|
| 2023/11 | DeepSeek-LLM 7B/67B | 中英双语通用大模型，Base/Chat 开源 |
| 2024/01 | DeepSeek-MoE 16B | 引入稀疏专家 MoE 路线 |
| 2024/05 | DeepSeek-V2 | MLA + DeepSeekMoE，支持长上下文 |
| 2024/09 | DeepSeek-V2.5 | 增强数据、路由、代码与对话能力 |
| 2024/12 | DeepSeek-V3 | 671B 总参数、约 37B 激活，成为 R1 基座 |
| 2025/01 | DeepSeek-R1 | 冷启动 + GRPO + 多阶段 RL 的推理模型 |

## DeepSeek 的低成本组合

DeepSeek 的成本优势不是来自单一技巧，而是组合：

1. **MoE**：总参数很大，但每 token 只激活少量专家。
2. **MLA**：压缩 KV Cache，降低长上下文推理显存和带宽压力。
3. **FP8**：V3 中使用低精度训练降低训练成本。
4. **MTP**：Multi-Token Prediction，提高训练效率。
5. **GRPO**：不用单独 value model，降低推理 RL 成本。

## V2 / V3 / R1 的区别

### DeepSeek-V2

关键词：

```text
MLA + DeepSeekMoE + 236B 总参数 + 约 21B 激活 + 长上下文
```

V2 的意义是把 DeepSeek 的核心架构路线定下来：用 MLA 优化 attention 侧的 KV Cache，用 MoE 优化 FFN 侧的参数容量。

### DeepSeek-V3

关键词：

```text
671B 总参数 + 约 37B 激活 + MLA + MoE + FP8 + MTP
```

V3 是更强的基座模型，也是 R1 的主要基础。

### DeepSeek-R1

关键词：

```text
V3-Base + 冷启动 SFT + GRPO + 多阶段 RL + 蒸馏
```

R1 的重点不是重做底层结构，而是通过后训练强化推理能力。

## 与 Transformer 的关系

DeepSeek 的底层仍然是 [[transformer/Transformer]] 路线，但做了现代化改造：

- Attention 侧：MLA，详见 [[deepseekArchitecture]] 和 [[transformer/inferenceOptimization|transformerAttentionOptimization]]。
- FFN 侧：DeepSeekMoE，详见 [[MoE]] 和 [[transformer/modernArchitectures|transformerMoEArchitecture]]。
- 后训练侧：R1 使用 GRPO、多阶段 RL，详见 [[deepseekR1]]。

## 关联连接

- [[deepseekArchitecture]] — DeepSeek MLA 与 MoE 架构
- [[deepseekR1]] — DeepSeek-R1 与 GRPO 推理训练
- [[MoE]] — 混合专家模型
- [[transformer/inferenceOptimization|transformerAttentionOptimization]] — MLA、GQA 等注意力优化
- [[LLMTraining]] — 预训练、SFT、RLHF/DPO/GRPO
- [[LLMInference]] — KV Cache、量化与部署优化

## 原始资料

- `raw/大模型学习/第2课-llama3-from-scratch/【拓展篇】万字详解Deepseek系列.pdf`
- `raw/大模型学习/第2课-llama3-from-scratch/【论文精读-3】Deepseek-R1论文精读.pdf`
