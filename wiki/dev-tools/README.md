---
type: cheatsheet
tags: [dev-tools, zsh, git, starship, 配置, 快捷键]
article_id: OBA-DEVTOOLS001
created_at: 2026/06/02
updated_at: 2026/06/02
---

# 开发工具配置指南

> 维护者：Flora
> 配置文件备份：`~/Documents/SoftwareConfiguration/dotfiles/`

---

## 📋 目录

- [概述](#概述)
- [配置文件位置](#配置文件位置)
- [快速开始](#快速开始)
- [文档索引](#文档索引)
- [快捷键速查](#快捷键速查)
- [常用命令速查](#常用命令速查)

---

## 概述

本文档记录了我的开发环境配置，涵盖以下工具：

- **[[Zsh]] Shell** — 命令行环境配置（[[Oh-My-Zsh]] + [[Starship]]）
- **[[Git]]** — 版本控制配置（别名、颜色、行为）
- **Gitignore** — 全局忽略规则
- **Vim / IdeaVim** — 编辑器配置

### 设计原则

1. **简洁优先** — 移除未使用的配置，保持清晰
2. **效率至上** — 添加常用别名和函数，减少重复输入
3. **安全第一** — 忽略敏感文件，使用安全的 Git 操作
4. **易于维护** — 按功能分组，添加详细注释

---

## 配置文件位置

```
~/.zshrc                              # Zsh 主配置
~/.config/starship.toml               # Starship 提示符配置
~/.gitconfig                          # Git 配置
~/.gitignore_global                   # Git 全局忽略规则
~/.vimrc                              # Vim 配置
~/.ideavimrc                          # IdeaVim 配置

~/Documents/SoftwareConfiguration/dotfiles/  # 配置备份目录
```

---

## 快速开始

### 1. 安装必要的工具

```bash
# Homebrew（macOS 包管理器）
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Oh My Zsh（Zsh 配置框架）
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Oh My Zsh 插件
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/agkozak/zsh-z $ZSH_CUSTOM/plugins/zsh-z

# Starship（现代化提示符）
brew install starship

# 可选：Delta（更好的 git diff 显示）
brew install git-delta

# 可选：trash（安全删除工具）
brew install trash
```

### 2. 应用配置

```bash
# 从备份目录复制配置
cp ~/Documents/SoftwareConfiguration/dotfiles/.zshrc ~/.zshrc
cp ~/Documents/SoftwareConfiguration/dotfiles/.gitconfig ~/.gitconfig
cp ~/Documents/SoftwareConfiguration/dotfiles/.gitignore_global ~/.gitignore_global

# 重新加载配置
source ~/.zshrc
```

### 3. 验证配置

```bash
# 检查 Starship 是否正常工作
starship --version

# 检查 Git 配置
git config --list
```

---

## 文档索引

### Zsh & Shell

📄 [[zshAndShell]]

- [[Oh-My-Zsh]] 配置
- [[Starship]] 提示符配置
- 常用别名（目录导航、Git、Maven、npm、Docker）
- 实用函数（mkcd、ff、port、serve 等）
- 插件说明和安装方法

### Git 配置

📄 [[gitConfig]]

- 基础配置（用户、推送、编辑器等）
- 20+ 常用别名
- 使用示例和工作流程
- 颜色配置

### Gitignore 配置

📄 [[gitignore]]

- macOS 系统文件
- IDE 和编辑器配置
- Java/Node.js/Python 项目规则
- 安全相关（密钥、环境变量）

### Git 基础

📄 [[Git]]

- Git 基础命令
- 分支管理
- 合并与冲突解决

---

## 快捷键速查

### Zsh 插件快捷键

> 由 [[zsh-autosuggestions]] 和 [[zsh-syntax-highlighting]] 插件提供

| 快捷键 | 功能 | 插件 |
|--------|------|------|
| `→` 或 `End` | 接受自动建议 | zsh-autosuggestions |
| `Ctrl+F` | 接受下一个单词 | zsh-autosuggestions |
| `Ctrl+E` | 接受整行建议 | zsh-autosuggestions |

**源码位置**：插件通过 `~/.zshrc` 中的 `plugins=(...)` 加载

```bash
# ~/.zshrc 第 79-84 行
plugins=(
  git                    # Git 命令补全和别名
  zsh-autosuggestions    # 命令历史建议
  zsh-syntax-highlighting  # 命令语法高亮
  zsh-z                  # 智能目录跳转
)
```

### Vim / IdeaVim 快捷键

> 详见 [[.ideavimrc]] 配置文件

| 快捷键 | 功能 | 说明 |
|--------|------|------|
| `Space` | Leader 键 | 前缀键 |
| `H` | 跳转到行首 | Normal 模式 |
| `L` | 跳转到行尾 | Normal 模式 |
| `J` | 向下翻半页 | Normal 模式 |
| `K` | 向上翻半页 | Normal 模式 |
| `Space + w` | 保存文件 | Normal 模式 |
| `Space + q` | 退出/关闭 | Normal 模式 |
| `Q` | 格式化文本 | Normal 模式 |

**源码**：`~/.ideavimrc`

```vim
" 设置 Leader 键为空格
let mapleader = " "

" 方向键映射
map L $
map H 0 
map J <C-D>
map K <C-U>

" 常用操作映射
map <leader>q <Action>(CloseEditor)
nmap <leader>w <Action>(SaveDocument)

" IDE 功能映射
map <leader>b <Action>(GotoDeclaration)  " 跳转到声明
map <leader>r <Action>(RenameElement)     " 重命名
map <leader>d <Action>(Debug)             " 开始调试
nmap f <Action>(AceAction)                " Ace Jump 快速跳转
```

### IdeaVim 专属快捷键

| 快捷键 | 功能 | 源码位置 |
|--------|------|----------|
| `Space + b` | 跳转到声明 | `map <leader>b <Action>(GotoDeclaration)` |
| `Space + i` | 切换布尔值 | `map <leader>i <Action>(ToggleBool)` |
| `Space + k` | 快速文档 | `map <leader>k <Action>(QuickJavaDoc)` |
| `Space + r` | 重命名 | `map <leader>r <Action>(RenameElement)` |
| `Space + d` | 开始调试 | `map <leader>d <Action>(Debug)` |
| `Space + w` | 保存文档 | `nmap <leader>w <Action>(SaveDocument)` |
| `f` | Ace Jump | `nmap f <Action>(AceAction)` |
| `Alt + J` | 向下移动行 | `map <A-J> <Action>(MoveLineDown)` |
| `Alt + K` | 向上移动行 | `map <A-K> <Action>(MoveLineUp)` |
| `]e` | 跳转到下一个错误 | `map ]e <Action>(GotoNextError)` |
| `[e` | 跳转到上一个错误 | `map [e <Action>(GotoNextError)` |

---

## 常用命令速查

### 目录导航

| 命令 | 说明 | 源码 |
|------|------|------|
| `projects` | 进入 ~/projects | `alias projects="cd ~/projects"` |
| `i` | 进入 ~/projects/i | `alias i="cd ~/projects/i"` |
| `r` | 进入 ~/projects/r | `alias r="cd ~/projects/r"` |
| `dl` | 进入 ~/Downloads | `alias dl="cd ~/Downloads"` |
| `doc` | 进入 ~/Documents | `alias doc="cd ~/Documents"` |
| `mkcd <dir>` | 创建并进入目录 | `function mkcd() { mkdir -p "$1" && cd "$1"; }` |
| `..` | 返回上级目录 | `alias ..="cd .."` |
| `...` | 返回上两级目录 | `alias ...="cd ../.."` |
| `....` | 返回上三级目录 | `alias ....="cd ../../.."` |

### Git 快捷命令

| 命令 | 说明 | 源码 |
|------|------|------|
| `gs` | git status | `alias gs="git status"` |
| `ga` | git add | `alias ga="git add"` |
| `gc` | git commit | `alias gc="git commit"` |
| `gp` | git push | `alias gp="git push"` |
| `gl` | git pull | `alias gl="git pull"` |
| `gd` | git diff | `alias gd="git diff"` |
| `glog` | 图形化日志 | `alias glog="git log --oneline --graph --decorate"` |
| `gac "msg"` | 添加所有并提交 | `function gac() { git add -A && git commit -m "$1"; }` |
| `gacp "msg"` | 添加、提交并推送 | `function gacp() { git add -A && git commit -m "$1" && git push; }` |

### Maven 快捷命令

| 命令 | 说明 | 源码 |
|------|------|------|
| `mvn-clean` | mvn clean | `alias mvn-clean="mvn clean"` |
| `mvn-build` | mvn clean package -DskipTests | `alias mvn-build="mvn clean package -DskipTests"` |
| `mvn-test` | mvn test | `alias mvn-test="mvn test"` |
| `mvn-run` | mvn spring-boot:run | `alias mvn-run="mvn spring-boot:run"` |

### npm 快捷命令

| 命令 | 说明 | 源码 |
|------|------|------|
| `ni` | npm install | `alias ni="npm install"` |
| `nid` | npm install --save-dev | `alias nid="npm install --save-dev"` |
| `nr` | npm run | `alias nr="npm run"` |
| `ns` | npm start | `alias ns="npm start"` |
| `nt` | npm test | `alias nt="npm test"` |

### Docker 快捷命令

| 命令 | 说明 | 源码 |
|------|------|------|
| `dps` | docker ps | `alias dps="docker ps"` |
| `dpsa` | docker ps -a | `alias dpsa="docker ps -a"` |
| `dc` | docker compose | `alias dc="docker compose"` |
| `dcup` | docker compose up -d | `alias dcup="docker compose up -d"` |
| `dcdown` | docker compose down | `alias dcdown="docker compose down"` |
| `dclogs` | docker compose logs -f | `alias dclogs="docker compose logs -f"` |

### 实用函数

| 命令 | 说明 | 源码 |
|------|------|------|
| `ff <name>` | 查找文件 | `function ff() { find . -name "*$1*" -type f; }` |
| `fd <name>` | 查找目录 | `function fd() { find . -name "*$1*" -type d; }` |
| `port <num>` | 查看端口占用 | `function port() { lsof -i :"$1"; }` |
| `serve [port]` | 启动 HTTP 服务器 | `function serve() { python3 -m http.server "${1:-8000}"; }` |

---

## Git 别名速查

> 详见 [[gitConfig]] 文档

### 状态和信息

| 别名 | 命令 | 说明 |
|------|------|------|
| `git st` | `git status -sb` | 简洁状态 |
| `git lg` | `git log --oneline --graph --decorate --all` | 图形化日志 |
| `git df` | `git diff` | 工作区差异 |
| `git dfs` | `git diff --staged` | 暂存区差异 |

### 分支操作

| 别名 | 命令 | 说明 |
|------|------|------|
| `git co` | `git checkout` | 切换分支 |
| `git cob` | `git checkout -b` | 创建并切换分支 |
| `git br` | `git branch` | 列出分支 |
| `git brd` | `git branch -D` | 删除分支 |

### 提交操作

| 别名 | 命令 | 说明 |
|------|------|------|
| `git cm` | `git commit` | 提交 |
| `git cma` | `git commit --amend` | 修改上次提交 |
| `git a` | `git add` | 添加文件 |
| `git aa` | `git add -A` | 添加所有文件 |
| `git unstage` | `git reset HEAD --` | 取消暂存 |

### 远程操作

| 别名 | 命令 | 说明 |
|------|------|------|
| `git f` | `git fetch` | 获取远程更新 |
| `git pl` | `git pull` | 拉取 |
| `git ps` | `git push` | 推送 |
| `git psf` | `git push --force-with-lease` | 安全强制推送 |

---

## 更新日志

### 2026-06-02

**优化内容：**

1. **解决主题冲突** — 移除 `ZSH_THEME="spaceship"`，统一使用 [[Starship]]
2. **清理 Zsh 配置** — 移除未使用的注释，按功能分组
3. **添加实用别名和函数** — 目录导航、Git、Maven、npm、Docker
4. **优化 Git 配置** — 添加 20+ 常用别名
5. **完善 Gitignore 规则** — 添加 macOS/IDE/Java/Node.js 规则
6. **同步配置备份** — 更新 dotfiles 目录

---

## 相关资源

- [[Oh-My-Zsh]] 官方文档：https://ohmyz.sh/
- [[Starship]] 官方文档：https://starship.rs/zh-CN/
- [[Git]] 官方文档：https://git-scm.com/doc
- Vim 官方文档：https://www.vim.org/docs.php
- IdeaVim 文档：https://github.com/JetBrains/ideavim
