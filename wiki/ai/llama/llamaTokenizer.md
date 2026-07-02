---
type: knowledge
tags: [AI, Llama, Tokenizer, BPE, Llama3]
article_id: OBA-llama-tokenizer
created_at: 2026/06/23
updated_at: 2026/06/23
---

# Llama 3 Tokenizer

[[llamaTokenizer]] 总结 `llama3-from-scratch-Copy1.ipynb` 中 Llama 3 tokenizer 的实现。Llama 3 的 tokenizer 使用 BPE 思路，并通过 tiktoken 风格的 `Encoding` 加载 mergeable ranks 与 special tokens。

## 基本流程

```text
文本 prompt
→ tokenizer.encode
→ token ids
→ 手动添加 <|begin_of_text|>
→ embedding table 查表
```

notebook 中使用：

```python
tokenizer_path = "Meta-Llama-3-8B/tokenizer.model"
mergeable_ranks = load_tiktoken_bpe(tokenizer_path)
tokenizer = tiktoken.Encoding(...)
```

## Special Tokens

notebook 中显式定义了 Llama 3 的特殊 token，包括：

- `<|begin_of_text|>`
- `<|end_of_text|>`
- `<|start_header_id|>`
- `<|end_header_id|>`
- `<|eot_id|>`，end of turn
- reserved special tokens

其中示例中：

```text
<|begin_of_text|> = 128000
```

这些 special tokens 对 chat template、角色标记、多轮对话边界非常重要。

## 正则切分规则

notebook 使用的 `pat_str`：

```python
r"(?i:'s|'t|'re|'ve|'m|'ll|'d)|[^\r\n\p{L}\p{N}]?\p{L}+|\p{N}{1,3}| ?[^\s\p{L}\p{N}]+[\r\n]*|\s*[\r\n]+|\s+(?!\S)|\s+"
```

它覆盖：

- 英文缩写：`'s`、`'re`、`'ve` 等。
- 字母词。
- 1 到 3 位数字。
- 标点和特殊符号。
- 换行和空白。

## 示例

prompt：

```text
the answer to the ultimate question of life, the universe, and everything is 
```

编码后：

```python
[128000, 1820, 4320, 311, 279, 17139, 3488, 315, 2324, 11, 279, 15861, 11, 323, 4395, 374, 220]
```

decode 后：

```python
['<|begin_of_text|>', 'the', ' answer', ' to', ' the', ' ultimate', ' question', ' of', ' life', ',', ' the', ' universe', ',', ' and', ' everything', ' is', ' ']
```

## 关键观察

- token 不等于自然语言单词。
- token 可能包含前导空格，例如 `' answer'`。
- special token 是词表的一部分。
- tokenizer 输出 token id，语义来自后续 [[llamaArchitecture|embedding 与模型权重]]。
- Llama 3 的词表大小是 `128256`。

## 与通用 Transformer Tokenization 的关系

[[transformer/foundations|transformerTokenization]] 讲通用分词算法；本页是 Llama 3 的具体实现案例：

- 同样属于子词分词路线。
- 使用 BPE mergeable ranks。
- 结合 special tokens 支撑 chat 格式。
- 直接决定输入序列长度和上下文占用。

## 关联连接

- [[llamaOverview]] — Llama 3 总览
- [[llamaArchitecture]] — Llama 3 架构
- [[llamaFromScratch]] — 从零推理流程
- [[transformer/foundations|transformerTokenization]] — 通用 Tokenization 知识
- [[transformer/foundations|transformerEmbedding]] — token id 到 embedding

## 原始资料

- `raw/大模型学习/第2课-llama3-from-scratch/llama3-from-scratch-Copy1.ipynb`
