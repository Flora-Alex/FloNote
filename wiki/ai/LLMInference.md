---
type: knowledge
tags:
  - AI
  - LLM
  - 推理
  - 部署
  - 量化
  - vLLM
article_id: OBA-ai-llm-inference
created_at: 2026/05/15
updated_at: 2026/05/15
---

# LLM 推理与部署

> [[LLM]] 从训练完成到实际提供服务，中间横跨解码策略、缓存机制、量化压缩、部署框架四大环节。本文系统梳理推理优化的核心知识与工程实践。

## 解码策略

回答的问题：模型每步输出概率分布，怎么选下一个 token？

### 确定性策略

- **贪心解码**：每步选概率最高的 token，极简可复现但复读机问题严重
- **Beam Search**：保留 B 条候选路径选总概率最高，与推理优化不兼容

### 随机性策略

- **Temperature 采样**：缩放概率分布锐度，T=0 等价贪心，T=1 原始分布
- **Top-K 采样**：固定截断只从概率最高 K 个 token 里采样
- **Top-P（Nucleus）采样**：自适应截断累加概率到 P 为止

### 为什么 LLM 时代 Beam Search 失宠

- 生成式任务没有"最优答案"，Beam Search 输出最 boring
- 与 [[Transformer]] 中的 KV Cache 不兼容（维护 B 条前缀显存爆炸）

### 场景选型表

| 任务 | 推荐策略 | 参数 |
|---|---|---|
| 代码生成 | 贪心/低温 | T=0~0.2 |
| SQL/JSON 抽取 | 贪心 | T=0 |
| 数学推理 | 贪心/Self-Consistency | T=0 或 0.7 |
| 日常对话 | Top-P 采样 | T=0.7, Top-P=0.9 |
| 创意写作 | Top-P 采样 | T=1.0~1.2, Top-P=0.95 |

### 进阶策略

- **推测解码**：小模型起草 + 大模型验证，速度提升 2-3 倍
- **Self-Consistency**：多次采样 + 多数投票，数学推理准确率提升 5-15%

## Temperature / Top-P / Top-K 最佳实践

- **核心原则**：一次主要调一个参数
- **Top-P vs Top-K**：Top-P 自适应（确定性问题候选池 1 个词，开放问题几十个词）
- **常见踩坑**：同时调高 temperature + 调低 top_p 互相打架

## KV Cache

- **问题**：自回归生成每步重算前面所有 token 的 attention，总计算量 O(N^3)
- **原理**：缓存前面所有 token 的 K 和 V，每步只算新 token 的 Q/K/V
- 计算量从 O(N^3) 降到 O(N^2)
- **显存代价公式**：KV Cache 显存 = 2 * num_layers * hidden_size * seq_len * batch_size * dtype_bytes

## Prompt Caching

- 与 KV Cache 是同一套缓存思路在两个时间尺度的延伸
- **KV Cache**：单次推理内复用；**Prompt Caching**：跨请求复用相同前缀
- Claude 显式标记 `cache_control` 断点；OpenAI 自动缓存（前缀超 1024 tokens）
- **价格优势**：缓存命中 token 费用 = 正常输入的 10%
- **适合场景**：固定 System Prompt、基于同一份长文档的多次问答、大量 Few-shot 示例
- **工程陷阱**：固定内容在前动态内容在后；缓存时效性

## 大模型量化

- **本质**：FP16 -> INT8/INT4，核心机制 scale + zero_point 线性映射
- **收益**：7B 模型 FP16 14GB -> INT4 3.5GB（1/4），吞吐高 2-4 倍
- **精度边界**：INT8 几乎无损（<0.5%），INT4 损失 1-3%（主流甜蜜点），INT3 损失 5-10%

### 三大量化算法

- **GPTQ**：逐层量化 + 误差补偿，数学严谨但量化耗时长
- **AWQ**：保护 1% 显著权重（承担 99% 输出贡献），推理速度快（比 GPTQ 快 1.5-2x）
- **QLoRA NF4**：非均匀量化按正态分布排列，配合 LoRA 微调

### 选型表

| 场景 | 推荐方案 |
|---|---|
| 生产部署看重速度 | AWQ/GPTQ/FP8 |
| 生产部署追求精度 | FP16/GPTQ INT4 |
| 个人微调消费级 GPU | QLoRA NF4 |
| 边缘部署 | GGUF Q4_K_M |
| CPU 部署 | llama.cpp + GGUF |

- 量化算法（GPTQ/AWQ）vs 文件格式（GGUF/safetensors）是两层东西

## 部署框架

### 五大框架对比

| 框架 | 核心创新 | 最佳场景 |
|---|---|---|
| vLLM | PagedAttention + Continuous Batching | 高吞吐 LLM API |
| SGLang | RadixAttention（共享前缀树） | Agent/多轮/Few-shot |
| TGI | HF 生态集成 | 企业级 + HF 生态 |
| llama.cpp | C++ 重写 + GGUF 量化 | CPU/Mac/边缘 |
| TensorRT-LLM | NVIDIA 硬件极致优化 | 大厂 NVIDIA 集群 |

### 核心技术解析

- **vLLM**：PagedAttention（模仿 OS 虚拟内存，显存利用率 30-40% -> 90%+）、Continuous Batching（吞吐率比 static batching 高 3-5 倍）
- **SGLang**：RadixAttention（相同前缀只存一份，省 50-80%），[[AIAgent]] 场景比 vLLM 首 token 延迟降低 2-3 倍
- **llama.cpp**：GGUF 格式、SIMD 优化、Metal 后端（苹果 M 系列统一内存架构）

### 选型决策

- 生产高吞吐 API -> vLLM
- Agent/多轮对话/Few-shot -> SGLang
- 拥抱 HF 生态企业级 -> TGI
- 本地/Mac/边缘/无 GPU -> llama.cpp
- 极致性能自家定制 -> TensorRT-LLM

### 四大误用陷阱

1. 用 vLLM 跑 Agent 多轮对话（缺 RadixAttention，应选 SGLang）
2. 用 llama.cpp 做高并发服务（应选 GPU + vLLM）
3. 用 TGI 追求绝对性能（应优先 vLLM/SGLang）
4. 用 TensorRT-LLM 做快速 POC（需编译 engine）

---

## 双向链接

- [[LLM]] - 大语言模型基础
- [[Transformer]] - 注意力机制与模型架构
- [[LLMTraining]] - 训练阶段的技术细节
- [[RAG]] - 检索增强生成
- [[AIAgent]] - AI 智能体（与 SGLang 部署场景紧密相关）
