---
type: knowledge
tags: [项目, dotfiles, Vim, Zsh, Git, 终端, 开发环境, 配置管理]
article_id: OBA-2026060305
created_at: 2026/06/03
updated_at: 2026/06/03
---

# Dotfiles — macOS 个人开发环境配置

> 以 Git 版本管理的 macOS 个人开发环境配置集。覆盖 Zsh、Git、Vim、IdeaVim、Starship、Ghostty、npm 共 9 个配置文件。结构清晰、文档完整，v1.0 版本。

## 配置清单

| 配置文件 | 工具 | 核心内容 |
|---|---|---|
| `.zshrc` | Zsh + Oh My Zsh | 4 个插件、大量别名、5 个实用函数、`.local` 分离 |
| `.gitconfig` | Git | push/pull 策略、20+ 别名、Delta 分页器、rerere |
| `.gitignore_global` | Git 全局忽略 | macOS/JetBrains/VSCode/多语言构建产物/敏感文件 |
| `.vimrc` | Vim | Leader=Space、行号、H/L/J/K 重映射 |
| `.ideavimrc` | IdeaVim | Vim 键位 + IDE Action（跳转声明/重命名/调试/AceJump） |
| `starship.toml` | Starship 提示符 | 多语言版本检测、Git 状态、自定义符号 |
| `config.ghostty` | Ghostty 终端 | Kanagawa 配色、分屏/标签页、Quick Terminal |
| `.npmrc` | npm | 国内镜像源、关闭 SSL 验证 |

## 设计理念

### 1. 分层架构思想

以 Zsh 为例的三层架构：

```
基础层：Oh My Zsh 框架 + 插件系统
扩展层：别名、函数、环境变量
本地层：~/.zshrc.local（机器特定配置）
```

`.local` 分离模式让主配置可安全同步分享，同时保留机器差异。

### 2. 跨工具键位一致性

**统一 Leader 键为 Space**：`.vimrc` 和 `.ideavimrc` 都使用 `let mapleader = " "`

**统一 H/L/J/K 映射**：
- H → 行首、L → 行尾
- J/K → 半页翻页（而非默认的行移动）

确保终端 Vim 和 JetBrains IDE 中的肌肉记忆完全一致。

### 3. 渐进式可选配置

大量使用注释标记可选功能，"注释即文档、取消注释即启用"：
- `.gitconfig` 中 Delta 分页器以注释预配置
- `.zshrc` 中 nvm/pyenv/Java/Go 等环境管理器按需启用
- Ghostty 中背景透明度、毛玻璃效果注释预留

### 4. 安全导向设计

| 措施 | 实现 |
|---|---|
| 防误删 | `alias rm="trash"` |
| 安全强推 | `git psf = push --force-with-lease` |
| 剪贴板保护 | `clipboard-paste-protection = true` |
| 敏感文件忽略 | `.gitignore_global` 忽略 `.env`/`.pem`/`.key` |
| 冲突复用 | `rerere.enabled = true` 自动记录冲突解决 |

## Shell 别名设计策略

三层逻辑，记忆成本极低：

| 层级 | 示例 | 说明 |
|---|---|---|
| 高频操作用最短缩写 | `gs` `ga` `gc` `gp` `gl` `gd` | 两个字母，几乎不需思考 |
| 组合操作封装为函数 | `gac()` = add -A + commit | `gacp()` = add + commit + push |
| 框架命令保留语义前缀 | `mvn-build` `dcup` `ni` | 语义明确 |

## Starship 项目感知提示符

通过 `detect_extensions` 和 `detect_files` 配置，提示符自动感知当前项目类型：

- 进入 Java 项目 → 自动显示 Java 版本
- 进入 Node 项目 → 自动显示 Node 版本
- 进入 Docker 目录 → 自动显示 Docker 上下文

**零配置适应** — 无需手动切换。

## Ghostty 键盘流设计

构建完整的键盘驱动工作流，不依赖鼠标：

| 功能 | 快捷键 |
|---|---|
| 新标签页 | Cmd+T |
| 关闭标签页 | Cmd+W |
| 水平分屏 | Cmd+D |
| 分屏导航 | Cmd+Alt+方向键 |
| Quick Terminal | Option+Space（全局下拉） |

## 相关链接

- 工具详情：[[zshAndShell]], [[gitConfig]], [[ghostty]], [[macSetup]]
- 原始代码：`raw/i/dotfiles/`
