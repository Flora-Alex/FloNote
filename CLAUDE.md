本项目是一个基于 [Karpathy 的 LLM Wiki 理念](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 构建的 Obsidian 知识库。
## 语言设定与核心角色 (Global Rules)
- **语言指令**：无论输入何种语言，你必须始终使用**简体中文**进行思考、回复和知识库的编写。
- **角色定义**：你正在维护一个 [[LLM Wiki]]（根据 [Karpathy 的规范](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f))，你的任务是将碎片化的信息编译成结构化、高度相互链接的 Obsidian 知识库。
- 这是一个由 LLM (Claude Code) 辅助维护的个人知识库。LLM 充当“程序员”，知识库是“代码库”。
## 目录结构与用途

```text 
Vault/
├── raw/                 # 原始资料（不可变层）。LLM 只读，永不修改。新摄入内容放此处。
├── wiki/                # LLM 编译维护层。LLM 可创建、编辑、拆分、合并文章。
│   ├── ai/              # AI 技术（LLM, GPT, transformer, 机器学习, agent...）
│   ├── claude/          # Claude 生态（Claude Code, Skills, MCP, hooks...）
│   ├── tauri/           # Tauri（桌面应用, tauri-app, Sidecar...）
│   ├── dev-tools/       # 开发工具（VSCode, IDE, CLI, Git, Zed...）
│   ├── backend/         # 后端开发（Java, Springboot, Redis, Go, Python...）
│   ├── current-affairs/ # 时事分析（经济, 政治, 金融, 投资...）
│   ├── career/          # 职业发展（职业规划, 个人画像, 理财规划, 协作计划...）
│   ├── alfred/          # 效率工具（Alfred 使用指南, 快捷键, 工作流...）
│   ├── obsidian/        # Obsidian（知识管理, 笔记, 双链...）
│   ├── projects/        # 项目开发经验沉淀（ob-project-log 自动生成）
│   ├── distill/         # 提炼后的决策知识（供 CLAUDE.md 读取，做 trade-off 决策）
│   ├── open-source/     # 开源仓库知识沉淀（软链接 + 预消化内容，CLAUDE.md 优先查阅）
│   └── {新主题}/         # AI 匹配不到预定义主题时，自动创建新目录 (camelCase 英文命名)
├── work/                # 工作相关（隔离区）。LLM 只读不写，不入全局索引。
├── interview/           # 面试题库。保持原有结构，LLM 只维护子索引。
├── 机密项目/             # 不碰，不入库，不索引，不编辑。
├── index.md             # 全局索引。LLM 每次编译后必须更新。
└── log.md               # 变更日志。只追加，不修改历史条目。
```
## 核心目录与权限边界
你必须严格遵守以下文件操作权限，这是不可逾越的底线：

| 目录         | 读取  | 写入     | 删除  | 索引  | 备注          |
| ---------- | --- | ------ | --- | --- | ----------- |
| `机密项目/`    | ❌   | ❌      | ❌   | ❌   | 绝对禁止        |
| `raw/`     | ✅   | ❌      | ❌   | ✅   | 不可变真相源      |
| `wiki/`    | ✅   | ✅      | ✅   | ✅   | 主工作区        |
| `work/`    | ✅   | ❌      | ❌   | ❌   | 隔离区，不入索引    |
| `log.md`   | ✅   | ⚠️ 仅追加 | ❌   | —   | Append-only |
| `index.md` | ✅   | ✅      | ❌   | —   | 每次编译后必更新    |

## Wiki 核心文件契约 

当你在 `/wiki/` 中工作时（尤其是执行写入操作后），必须维护以下基石：

1. **`wiki/index.md` (总目录)**：
	根据实际内容给出的全局索引

2. **`wiki/log.md` (操作日志)**：
    只能追加写入（Append-only）。
    每次操作后记录：`## [YYYY-MM-DD] <动作> | <操作简述>`。
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
所有生成的 wiki 页面必须包含以下 YAML 头部(其中created_at，last_updated的类型均为date)：

```yml
---
type: summary | topic | cheatsheet | knowledge | (AI可根据具体情况设定)
tags: [知识标签]
article_id: OBA-xxxxxxxx
created_at: YYYY/MM/DD
updated_at: YYYY/MM/DD
---
```

## 文件命名规范

### 命名格式

**统一使用小驼峰命名（camelCase）**，不使用短横线（kebab-case）或下划线（snake_case）。

**规则**：
1. 首字母小写
2. 每个新单词首字母大写
3. 不使用分隔符（短横线、下划线）
4. 文件夹前缀不重复（如 `alfred/` 下的文件不需要再加 `alfred-` 前缀）

**示例**：
```
✅ 正确：
- gettingStarted.md
- basicFeatures.md
- floraProfile.md
- careerPlan.md

❌ 错误：
- alfred-getting-started.md（冗余前缀 + 短横线）
- flora-profile.md（短横线）
- career_plan.md（下划线）
```

### 特殊文件

以下文件保持大写，不遵循驼峰命名：
- `README.md` — 入口文档
- `CLAUDE.md` — 协作规范
- `AGENT.md` — Agent 规范

### 目录结构示例

```text
wiki/
├── ai/
│   ├── LLM.md
│   ├── transformer.md
│   └── ...
├── career/
│   ├── README.md
│   ├── floraProfile.md
│   ├── careerPlan.md
│   ├── financialPlan.md
│   └── collaborationPlan.md
├── alfred/
│   ├── README.md
│   ├── gettingStarted.md
│   ├── basicFeatures.md
│   ├── shortcuts.md
│   ├── advancedFeatures.md
│   ├── workflows.md
│   └── tips.md
└── ...
```