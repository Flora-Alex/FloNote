本项目是一个基于 [Karpathy 的 LLM Wiki 理念](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 构建的 Obsidian 知识库。
## 语言设定与核心角色 (Global Rules)
- **语言指令**：无论输入何种语言，你必须始终使用**简体中文**进行思考、回复和知识库的编写。
- **角色定义**：你正在维护一个 [[LLM Wiki]]（根据 [Karpathy 的规范](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f))，你的任务是将碎片化的信息编译成结构化、高度相互链接的 Obsidian 知识库。
- 这是一个由 LLM (Claude Code) 辅助维护的个人知识库。LLM 充当“程序员”，知识库是“代码库”。
## 目录结构与用途

```text 
raw/ 原始资料（不可变层）。LLM 只读，永不修改。新摄入内容放此处。 
wiki/ LLM 编译维护层。LLM 可创建、编辑、拆分、合并文章。 
  ai/ AI 技术（LLM, GPT, transformer, 机器学习, agent...） 
  claude/ Claude 生态（Claude Code, Skills, MCP, hooks...） 
  tauri/ Tauri（桌面应用, tauri-app, Sidecar...） 
  dev-tools/ 开发工具（VSCode, IDE, CLI, Git, Zed...） 
  backend/ 后端开发（Java, Springboot, Redis, Go, Python...） 
  current-affairs/ 时事分析（经济, 政治, 金融, 投资...）
  career/ 职业发展（面试, 求职, 职业规划...） 
  obsidian/ Obsidian（知识管理, 笔记, 双链...） 
  {新主题}/ AI 匹配不到预定义主题时，自动创建新目录 (kebab-case 英文命名) 
  distill/ 提炼后的决策知识（供 CLAUDE.md 读取，做 trade-off 决策） 
  open-source/ 开源仓库知识沉淀（软链接 + 预消化内容，CLAUDE.md 优先查阅）      projects/ 项目开发经验沉淀（ob-project-log 自动生成） 
work/ 工作相关（隔离区）。LLM 只读不写，不入全局索引。 
interview/ 面试题库。保持原有结构，LLM 只维护子索引。 
机密项目/ 不碰，不入库，不索引，不编辑。 
index.md 全局索引。LLM 每次编译后必须更新。 
log.md 变更日志。只追加，不修改历史条目。
```
## 核心目录与权限边界
你必须严格遵守以下文件操作权限，这是不可逾越的底线：

### 🔴 禁止触碰区 (Forbidden)
- `机密项目/`：
  - **绝对禁止**访问、读取、写入、索引、编辑此目录下的任何文件。
  - 在任何情况下都不得在输出中提及或引用此目录的内容。
  - 即使路径被意外传入，也必须立即拒绝并提醒用户。

### 🟠 只读区 (Read-Only)
- `/raw/` (不可变层 - Immutable)：
  - **绝对只读**。这里存放我的原始素材、网页剪藏和自媒体文案。
  - **禁止修改或删除此目录下的任何文件**。它是事实的唯一真相来源。
  - LLM 只可引用其中的内容来编译 wiki 页面，但绝不能改动原文。
  - 新摄入的原始资料由用户手动放入，LLM 不负责写入此目录。
- `/work/` (隔离区 - Isolated)：
  - **只读不写**。这里存放工作相关的敏感内容。
  - LLM 可以读取以回答问题，但**禁止创建、编辑、删除**任何文件。
  - 此目录内容**不入全局索引**（index.md），不参与 wiki 双链网络。
  - LLM 不得将此目录内容泄露到 wiki/ 或其他可写区域。

### 🟡 受限写入区 (Restricted Write)
- `/interview/` (索引维护区 - Index-Only)：
  - **保持原有结构**。LLM 不得重组、移动、删除已有的面试题文件。
  - LLM **只可维护子索引**（如创建分类索引文件），不可修改原始题目内容。
  - 新增面试题时，必须遵循已有的文件命名和分类规范。
- `/distill/` (决策知识层 - Decision Layer)：
  - **谨慎写入**。此目录存放提炼后的决策知识，供 CLAUDE.md 读取做 trade-off 决策。
  - 写入前必须确认内容已充分提炼，非碎片化信息。
  - 每次写入必须附带来源溯源（指向 raw/ 或 wiki/ 中的原始出处）。
  - **禁止删除**已有条目，如需更新应追加新版本并标注日期。
- `log.md` (变更日志 - Append-Only)：
  - **只追加写入**，永远不修改或删除历史条目。
  - 每次操作后必须记录，格式见 Wiki 核心文件契约。

### 🟢 自由写入区 (Full Write Access)
- `/wiki/` (编译维护层 - Primary Workspace)：
  - LLM 的**主要工作区**。可创建、编辑、拆分、合并文章。
  - 写入操作后必须同步更新 `wiki/index.md`。
  - 子目录分类规则：
    - `ai/` — AI 技术（LLM, GPT, transformer, 机器学习, agent...）
    - `claude/` — Claude 生态（Claude Code, Skills, MCP, hooks...）
    - `tauri/` — Tauri（桌面应用, tauri-app, Sidecar...）
    - `dev-tools/` — 开发工具（VSCode, IDE, CLI, Git, Zed...）
    - `backend/` — 后端开发（Java, Springboot, Redis, Go, Python...）
    - `current-affairs/` — 时事分析（经济, 政治, 金融, 投资...）
    - `career/` — 职业发展（面试, 求职, 职业规划...）
    - `obsidian/` — Obsidian（知识管理, 笔记, 双链...）
    - `{新主题}/` — 匹配不到预定义主题时，自动创建新目录（kebab-case 英文命名）
    - `open-source/`—存放开源仓库的预消化内容和软链接。
    - `projects/`—存放项目开发经验沉淀。
  - 所有页面必须遵守 Frontmatter 规范和双向链接契约。
- `index.md` (全局索引 - Global Index)：
  - LLM **每次编译后必须更新**此文件。
  - 按分类组织所有 wiki 页面条目，确保无遗漏。

### 权限速查表
| 目录 | 读取 | 写入 | 删除 | 索引 | 备注 |
|------|------|------|------|------|------|
| `机密项目/` | ❌ | ❌ | ❌ | ❌ | 绝对禁止 |
| `raw/` | ✅ | ❌ | ❌ | ✅ | 不可变真相源 |
| `work/` | ✅ | ❌ | ❌ | ❌ | 隔离区，不入索引 |
| `interview/` | ✅ | ⚠️ 仅索引 | ❌ | ✅ | 保持原有结构 |
| `distill/` | ✅ | ⚠️ 谨慎 | ❌ | ✅ | 需附带来源溯源 |
| `log.md` | ✅ | ⚠️ 仅追加 | ❌ | — | Append-only |
| `wiki/` | ✅ | ✅ | ✅ | ✅ | 主工作区 |
| `open-source/` | ✅ | ✅ | ⚠️ | ✅ | 优先查阅层 |
| `projects/` | ✅ | ✅ | ⚠️ | ✅ | 不得覆盖原始记录 |
| `index.md` | ✅ | ✅ | ❌ | — | 每次编译后必更新 |

## Wiki 核心文件契约 
当你在 `/wiki/` 中工作时（尤其是执行写入操作后），必须维护以下基石：

1. **`wiki/index.md` (总目录)**：
	根据实际内容给出的全局索引

2. **`wiki/log.md` (操作日志)**：
    只能追加写入（Append-only）。每次操作后记录：`## [YYYY-MM-DD] <动作> | <操作简述>`。
    操作类型： ingest, query, lint, sync
    范例：
    ```markdown
    ## [2026-04-11] ingest | 引入项目 Claude Code 核心概念
    - **变更**: 新增 [[ClaudeCode]], [[摘要-claude-code-docs]]; 更新 [[index.md]]
    - **冲突**: 无 (或: 冲突 [[RAG架构]], 已标注)

    ## [2026-04-11] query | 解析 Karpathy LLM-Wiki 理念
    - **输出**: 已保存至 [[分析-karpathy-wiki-philosophy]]

    ## [2026-04-11] lint | 周度健康检查
    - **结果**: 修复 2 处死链，发现 1 个孤儿页面 [[UnlinkedPage]]
    ```
3. **强制双向链接**：
   在相关概念第一次出现时使用 Obsidian 双链 `[[页面名称]]` 链接到对应的相关概念。绝不能产生孤岛页面。
4. **矛盾处理原则**：
   如果新摄入的知识与旧知识冲突，不要静默覆盖。在页面中新建 `## 知识冲突` 区块，将两种说法都保留并做对比。

## 工作流指令说明 (Workflows / Skills)

**未来开发**

## 页面 Frontmatter (YAML) 规范
所有生成的 wiki 页面必须包含以下 YAML 头部：
---
type: summary | topic | cheatsheet | knowledge | (AI可根据具体情况设定)
tags: [知识标签]
article_id: OBA-xxxxxxxx
created_at: YYYY/MM/DD
last_updated: YYYY/MM/DD
