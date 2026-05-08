---
type: knowledge
tags:
  - AI
  - Agent
  - Workflow
  - 工作流
  - 流程编排
article_id: OBA-ai-workflow
created_at: 2026/05/08
updated_at: 2026/05/08
---

### 什么是 Workflow？

Workflow（工作流）是一个确定性的流程编排框架，把 [[AIAgent|Agent]]、LLM、[[ToolCalling|Tools]] 组织成一条固定的执行链路。每个节点做什么、按什么顺序流转、走哪个分支，都是开发者事先在代码里写死的。

Workflow 和 Agent 最核心的区别是：**谁在做「下一步去哪」这个决策？**

- **Agent**：LLM 自己决定下一步做什么，是动态的
- **Workflow**：开发者在代码里写死流程，是确定的

### Workflow 的核心特点

**确定性高**：行为完全可预测，你在代码里看到什么，它就做什么，不会有任何「惊喜」。

**可控性强**：生产环境里出了问题，可以打断点逐步追，精确定位是哪个节点出了故障。

**调试友好**：链路清晰，可逐步追踪，每个节点的输入输出都明确。

**灵活性低**：流程提前写死，遇到你没预料到的情况就会走入死胡同。

### Workflow 的典型结构

```python
def run_customer_service_workflow(user_query: str) -> str:
    # ---- 第一步：意图识别 ----
    # LLM 只负责判断类别，「下一步去哪」是下面的 if/elif 决定的
    intent = classify_intent_with_llm(user_query)  # 返回 "product" / "refund" / "other"

    # ---- 第二步：根据意图走不同分支 ----
    # 这个分支判断是开发者写的 Python 代码，不是 LLM 的决策
    if intent == "product":
        # 产品问题：去知识库检索，再生成回答
        docs = search_knowledge_base(user_query)
        answer = generate_answer_with_llm(user_query, docs)
        return answer

    elif intent == "refund":
        # 退款问题：查订单系统，再走审核流程
        order_info = query_order_system(user_query)
        if order_info["eligible"]:
            process_refund(order_info["order_id"])
            return "退款已受理，预计 3 个工作日到账"
        else:
            return "很抱歉，该订单不满足退款条件"

    else:
        # 其他问题：转人工
        escalate_to_human_agent(user_query)
        return "已为您转接人工客服，请稍候"
```

在这个例子里，LLM 出现了两次（意图分类、生成回答），但它只是流程里的两个工位，「接下来去哪」完全由 if/elif 这些普通 Python 代码控制。

### Workflow 的节点类型

Workflow 的节点可以是：

**LLM 节点**：直接调用 LLM 完成某个任务，比如意图分类、文本生成、摘要提取。

**Agent 节点**：嵌入一个完整的 Agent，让它自主决策完成某个子任务。

**Tool 节点**：直接调用工具函数，比如数据库查询、API 调用、文件读写。

**条件节点**：根据某个条件判断走哪个分支，比如 if/else、switch。

### 常见的 Workflow 编排模式

**Prompt Chaining（提示链）**：把一个大任务拆成多个小步骤，前一步的输出作为后一步的输入，像流水线一样串起来。

**Routing（路由）**：先用一个 LLM 做分类判断，然后根据分类结果把请求分发到不同的处理分支。前面客服系统的例子就是典型的路由模式。

**Parallelization（并行化）**：把可以同时进行的子任务并行执行，最后汇总结果。这在需要多维度分析的场景下特别有用，比如同时从多个数据源检索信息。

**Orchestrator-Workers（编排者-工人）**：一个中央编排者负责分配任务，多个 Worker 各自完成子任务，适合任务可以分解但子任务之间相互独立的场景。

**Evaluator-Optimizer（评估者-优化者）**：一个 LLM 负责生成输出，另一个 LLM 负责评估质量，如果不通过就把反馈给回生成者，让它改进后重新输出，循环直到通过或达到最大重试次数。

### Workflow vs Agent

| 维度 | Workflow | Agent |
| --- | --- | --- |
| 决策者 | 开发者（硬编码流程） | LLM（动态决策） |
| 确定性 | 高，行为完全可预测 | 低，同输入可能走不同路径 |
| 灵活性 | 低，流程固定 | 高，能处理预料之外的情况 |
| 调试难度 | 容易，链路清晰 | 困难，行为不确定 |
| 适用场景 | 流程相对固定的业务 | 需要灵活判断的复杂任务 |
| 成本可控性 | 高，每步 token 预算精确 | 低，可能无限循环 |

### Agentic Workflow

实际工程里最主流的模式是 **Agentic Workflow**：**用 Workflow 固定主流程的骨架，在需要灵活判断的节点嵌入 Agent，其余固定节点直接用 LLM 或 Tools。**

- 骨架是确定的，让你能控制整体行为、便于调试
- 关键节点是灵活的，让你能应对各种复杂情况
- 两个优点都有，两个缺点都被削弱了

Anthropic 在他们的 Agent 工程实践中总结了一个非常实用的原则：**能用 Workflow 解决的问题，就不要用 Agent。**

原因是在生产环境里，可控性比灵活性更重要。Workflow 的行为是确定的，出了问题你能精确定位是哪个节点出了错。而 Agent 的行为是概率性的，同样的输入可能走不同的路径，测试覆盖率天然就低。

建议的策略是：先从最简单的 Workflow 开始，只有当你发现某个节点确实需要灵活决策、写死的逻辑无法覆盖所有情况时，才把那个节点升级成 Agent。

### Workflow 的性能优势

纯 Agent 模式下，一个复杂任务可能需要 LLM 跑十几轮甚至几十轮决策循环，每轮都要把完整的上下文发给模型，token 消耗是线性增长的，延迟也会累积。

而 Workflow 模式因为流程是固定的，你可以：
- 精确控制每个节点的 token 预算
- 不需要的上下文不传
- 该并行的步骤并行执行

整体的延迟和成本都更可控。

### 什么时候用 Workflow？

**适合用 Workflow 的场景**：
- 业务流程相对固定，每一步的逻辑都很清楚
- 需要高可控性和可预测性
- 生产环境，出了问题需要快速排查
- 成本敏感，需要精确控制 token 消耗

**不适合用 Workflow 的场景**：
- 任务边界不明确，需要大量探索
- 每一步的决策逻辑太复杂，写死规则成本太高
- 需要应对大量预料之外的情况

### 主流 Workflow 框架

**LangGraph**：LangChain 团队推出的 Workflow 编排框架，支持复杂的分支、循环、并行逻辑，是目前最主流的选择之一。

**LlamaIndex Workflows**：LlamaIndex 提供的 Workflow 解决方案，和 LlamaIndex 的 RAG 能力深度集成。

**Temporal**：工业级的 Workflow 引擎，支持长时间运行的 Workflow、故障恢复、重试机制等生产级特性。

**自研**：对于简单的 Workflow，直接用 Python 代码写 if/else、for 循环往往更清晰，不需要引入框架的复杂度。
