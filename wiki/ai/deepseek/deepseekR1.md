---
type: knowledge
tags: [AI, DeepSeek, DeepSeekR1, GRPO, 强化学习, 推理模型]
article_id: OBA-deepseek-r1
created_at: 2026/06/23
updated_at: 2026/06/23
---

# DeepSeek-R1

[[deepseekR1]] 总结 `【论文精读-3】Deepseek-R1论文精读.pdf` 的核心知识。DeepSeek-R1 的重点是用强化学习激发推理能力：R1-Zero 验证纯 RL 可激发推理，R1 则通过冷启动和多阶段训练得到工程可用的推理模型。

## R1 与 R1-Zero

### R1-Zero

R1-Zero 的核心实验：

```text
DeepSeek-V3-Base → 直接 RL / GRPO → DeepSeek-R1-Zero
```

特点：

- 不经过传统 SFT。
- 直接用强化学习训练推理能力。
- 出现长链推理、自我验证、反思等现象。
- 证明 RL 可以激发 LLM 的推理能力。

问题：

- 可读性差。
- 容易中英混杂。
- 推理格式不稳定。
- 用户体验不足。

### R1

R1 是工程可用路线：

```text
DeepSeek-V3-Base
→ 冷启动 SFT
→ 推理导向 RL / GRPO
→ 拒绝采样生成新 SFT 数据
→ 全面 SFT
→ 全场景 RL
→ DeepSeek-R1
```

R1 的核心是让模型既会推理，又能用可读、稳定、对齐的方式输出。

## Aha Moment

R1-Zero 训练中出现所谓 Aha Moment：模型逐渐学会延长思考链、检查中间步骤、发现错误并回退修正。

可理解为：

```text
模型在 RL 奖励驱动下，学会用更长推理链换取更高正确率
```

## GRPO

GRPO，即 Group Relative Policy Optimization，是 R1 的关键 RL 算法。

它解决的问题：传统 PPO 通常需要额外 value model/critic，对大模型训练成本高。

GRPO 的思想：

1. 对同一个 prompt 采样一组回答。
2. 对每个回答计算 reward。
3. 用组内平均值和标准差做 baseline。
4. 用相对奖励估计 advantage。

核心形式：

```text
A_i = (r_i - mean(r)) / std(r)
```

优势：

- 不需要单独训练大规模 value model。
- 降低 RL 成本。
- 适合数学、代码等可验证任务。

## 冷启动

R1-Zero 证明纯 RL 可行，但输出质量不稳定。R1 引入冷启动 SFT，作用是：

- 规范推理格式。
- 提高可读性。
- 减少语言混杂。
- 给 RL 一个更好的初始输出分布。

冷启动不是为了提供全部推理能力，而是让模型先学会“怎么把推理过程写得像样”。

## 推理导向 RL

R1 的 RL 主要集中在：

- 数学推理。
- 代码推理。
- 逻辑推理。

这些任务适合 RL 的原因：

- 数学答案可验证。
- 代码可用测试用例验证。
- 奖励信号相对客观。
- 可以用规则奖励替代纯人工偏好。

## 拒绝采样

拒绝采样用于构造高质量 SFT 数据：

1. 对同一 prompt 生成多个候选回答。
2. 用规则、奖励模型或强模型过滤。
3. 保留正确、清晰、高质量的回答。
4. 回灌 SFT。

它连接了 RL 和 SFT：先让模型探索，再筛选优质推理轨迹监督学习。

## 蒸馏

DeepSeek-R1 很强但很大，因此使用蒸馏迁移推理能力。

流程：

```text
DeepSeek-R1 生成 reasoning traces
→ 过滤整理为 SFT 数据
→ 微调 Qwen / Llama 等小模型
```

关键点：

- 小模型不一定要自己从头 RL。
- 模仿强推理模型的轨迹通常更高效。
- 蒸馏把推理模式迁移到更低部署成本的模型上。

## R1 的完整训练流程速记

```text
V3-Base
→ 少量高质量冷启动 CoT 数据 SFT
→ GRPO 推理 RL
→ 拒绝采样生成更多 SFT 数据
→ 通用能力 SFT
→ 全场景 RL
→ R1
→ 蒸馏到小模型
```

## 面试要点

### R1 和 R1-Zero 的区别？

R1-Zero 是纯 RL 验证路线，展示推理能力可以被 RL 激发；R1 是工程路线，加入冷启动、多阶段 SFT/RL，解决可读性、语言混杂和通用能力问题。

### GRPO 相比 PPO 的优势？

GRPO 不需要额外 value model，而是用同一 prompt 下多个回答的组内相对奖励做 advantage，降低大模型 RL 成本。

### 为什么数学和代码适合训练推理模型？

因为答案可验证，reward 更客观，可以用规则奖励训练模型学会长链推理和自我检查。

### R1 蒸馏说明什么？

说明大模型通过 RL 学到的推理轨迹可以通过 SFT 迁移给小模型；对小模型来说，模仿高质量推理轨迹比自己探索更高效。

## 关联连接

- [[deepseekOverview]] — DeepSeek 系列总览
- [[deepseekArchitecture]] — V2/V3 架构基础
- [[LLMTraining]] — SFT、RLHF、GRPO、蒸馏
- [[agent/agentArchitectures|CoT]] — 思维链推理
- [[LLM]] — 大语言模型
- [[llama/llamaOverview]] — R1 蒸馏可迁移到 Llama 等模型

## 原始资料

- `raw/大模型学习/第2课-llama3-from-scratch/【论文精读-3】Deepseek-R1论文精读.pdf`
- `raw/大模型学习/第2课-llama3-from-scratch/【拓展篇】万字详解Deepseek系列.pdf`
