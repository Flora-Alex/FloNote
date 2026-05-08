---
type: knowledge
tags:
  - AI
  - Agent
  - ReAct
  - 推理模式
  - 工具调用
article_id: OBA-ai-react
created_at: 2026/05/07
updated_at: 2026/05/08
---

### 什么是 ReAct？

ReAct（Reasoning + Acting）是 [[AIAgent]] 最主流的设计范式之一，核心机制是把**推理（Reasoning）**和**行动（Acting）**交替进行，形成一个「思考 -> 行动 -> 观察」的循环。

ReAct 由 Yao 等人在 2022 年提出，解决了纯 [[CoT]]（思维链）只能纯文字推理、无法与外部世界交互的局限。

### ReAct 核心循环

每一轮 ReAct 循环由三个步骤组成：

**Thought（思考）**：LLM 先把当前的情况分析一遍，把推理过程写出来，比如「用户想查竞品信息，我应该先用搜索工具查一下竞品 A 的最新动态」。

**Action（行动）**：LLM 根据思考的结论决定调用哪个工具、传什么参数，比如 `Action: search, Action Input: 竞品 A 最新动态`。

**Observation（观察）**：工具执行后返回的结果被反馈给 LLM，它读取这个结果，然后进入下一轮 Thought，重新分析当前局面、决定接下来怎么做。

这个循环会不断重复，直到 LLM 在某轮 Thought 中判断「信息已经够了，可以给出最终答案」，整个 Agent 才停下来。

### 为什么需要 ReAct？

对比三种方案：

**纯 CoT**：只能在脑子里推理，推得再好也拿不到实时数据，遇到需要实时信息的场景就抓瞎，而且纯靠内部推理很容易产生幻觉。

**纯 Act-only**：让 LLM 直接输出工具调用序列，不写任何思考过程。问题在于每一步行动之间没有推理链条来连接，就像一个人闷头干活但不动脑子，遇到需要调整策略的情况就容易出错。

**ReAct**：把推理和行动交织在一起。Thought 帮助 LLM 分析当前局势、决定下一步该做什么，Action 让它把决策落地为真实操作，Observation 把外部世界的反馈带回来，三者互补形成闭环。

### ReAct 的实现原理

ReAct 是通过 prompt 格式来约束 LLM 的输出结构，但这个循环**不是 LLM 自己在转，而是由你的代码来驱动**的。

LLM 每次只做一件事：根据当前的历史，输出下一步的 Thought 加上 Action。你的代码负责：
1. 检测 LLM 的输出，判断「有没有 Final Answer」
2. 如果没有，解析出 Action、执行对应的工具
3. 把工具结果作为 Observation 填回历史
4. 再次调用 LLM，进入下一轮

典型的 ReAct prompt 格式：

```
你是一个 AI 助手，可以使用以下工具：
- search(query): 搜索互联网获取最新信息
- calculator(expr): 计算数学表达式

回答时请严格按照以下格式：
Thought: 你的思考过程（分析当前情况，决定下一步）
Action: 工具名称
Action Input: 工具的输入参数
Observation: （此行由系统填入工具返回的结果，你不用写）
... 以上可以重复多轮 ...
Final Answer: 当你确定可以回答时，在这里给出最终答案
```

### ReAct 的代码实现

```python
def react_agent(question: str, tools: dict, max_steps: int = 10):
    # 把 ReAct 格式约束和问题拼在一起，作为初始 prompt
    prompt = build_react_prompt(question, tools)
    # 用来存每一轮的对话历史
    history = []

    for _ in range(max_steps):
        # 调 LLM，让它输出下一步的 Thought + Action
        response = llm.generate(prompt + "\n".join(history))

        if "Final Answer:" in response:
            # LLM 判断任务完成了
            return response.split("Final Answer:")[-1].strip()

        # 从 LLM 输出里解析出 Action 名称和 Action Input
        action, action_input = parse_action(response)

        # 执行对应的工具，拿到真实结果
        if action in tools:
            observation = tools[action](action_input)
        else:
            observation = f"工具 {action} 不存在，请选择可用工具"

        # 把这一轮的 LLM 输出和 Observation 都追加进历史
        history.append(response)
        history.append(f"Observation: {observation}")

    return "超过最大步数，任务未完成"
```

现代 LLM（GPT-4、Claude 3 之后）基本都原生支持 **Function Calling / Tool Use**，模型可以直接输出结构化的 JSON 工具调用，不再需要靠解析 `Action: xxx` 这种文本格式。

### ReAct 的优势

- **实现简单**：只需要一个循环和工具解析逻辑
- **灵活度高**：每一步都能根据最新情况做决策
- **逻辑透明**：Thought 内容可见，出了问题可以直接看是在哪一步想歪了

### ReAct 的局限

**循环漂移**：ReAct 是「走一步看一步」的模式，每一步都是局部最优决策，处理特别复杂的、需要全局规划的任务时，容易在中间迷失方向，忘了最初的目标是什么。

**错误传播**：ReAct 的每一步决策都建立在前面所有步骤的结果之上，如果中间某一步拿到了错误的信息，后面所有的推理都会被这个错误带跑。而且 ReAct 没有内置的「回头检查」机制。

这两个问题的根源是同一个：ReAct 是纯粹的「前向推理」，没有全局规划来约束方向，也没有反思机制来纠正错误。

### ReAct vs Plan-and-Execute

| 维度 | ReAct | Plan-and-Execute |
| --- | --- | --- |
| 决策方式 | 走一步看一步，边想边干 | 先定完整计划，再按计划执行 |
| 全局视野 | 无，容易漂移 | 有，不容易跑偏 |
| 灵活性 | 高，能实时调整 | 较低，计划定死后调整成本高 |
| 实现复杂度 | 低 | 较高，需要规划器和执行器 |
| 适用场景 | 流程不固定、需要探索的任务 | 流程长、需要全局统筹的任务 |
| Token 消耗 | 线性增长，步骤越多越贵 | 规划一次，执行阶段可控 |

### 什么时候用 ReAct？

ReAct 适合：
- 任务步骤不多、每步都比较独立
- 流程不太固定、需要探索性地获取信息
- 需要快速原型验证的场景

如果任务很复杂、步骤之间有依赖关系需要全局统筹，考虑使用 [[AIAgent|Plan-and-Execute]] 模式。

实际工程中，两者经常混合使用：用 Plan-and-Execute 做整体规划，每个步骤内部用 ReAct 来执行。
