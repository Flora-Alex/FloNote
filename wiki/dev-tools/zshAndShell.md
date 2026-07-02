---
type: topic
tags: [dev-tools, zsh, oh-my-zsh, starship, shell, 终端]
article_id: OBA-DEVTOOLS002
created_at: 2026/06/02
updated_at: 2026/06/02
---

# Zsh & Shell 配置指南

> 配置文件位置：`~/.zshrc`
> 备份位置：`~/Documents/SoftwareConfiguration/dotfiles/.zshrc`

---

## 📋 目录

- [概述](#概述)
- [[Oh-My-Zsh]] 配置
- [[Starship]] 提示符
- [常用别名](#常用别名)
- [实用函数](#实用函数)
- [插件说明](#插件说明)
- [修改理由](#修改理由)
- [常见问题](#常见问题)

---

## 概述

我的 Zsh 配置基于以下组件：

- **[[Oh-My-Zsh]]** — Zsh 配置框架，提供插件管理和主题系统
- **[[Starship]]** — 现代化提示符（替代 Spaceship 主题）
- **插件** — 自动建议、语法高亮、智能跳转

### 配置文件位置

```
~/.zshrc                          # 主配置文件
~/.config/starship.toml           # Starship 提示符配置
~/Documents/SoftwareConfiguration/dotfiles/  # 备份目录
```

---

## Oh-My-Zsh 配置

### 基础设置

```bash
# ~/.zshrc 第 5-14 行

# Oh My Zsh 路径
export ZSH="$HOME/.oh-my-zsh"

# 主题：使用 Starship（在文件末尾初始化）
# 这里设置为空，避免与 Starship 冲突
ZSH_THEME=""
```

**说明**：`ZSH_THEME=""` 是为了解决与 [[Starship]] 的冲突。详见 [修改理由](#修改理由) 章节。

### 更新行为

```bash
# ~/.zshrc 第 17-18 行

zstyle ':omz:update' mode reminder    # 仅提醒更新，不自动更新
zstyle ':omz:update' frequency 13     # 每 13 天检查一次更新
```

### 插件配置

```bash
# ~/.zshrc 第 21-27 行

plugins=(
  git                    # Git 命令补全和别名
  zsh-autosuggestions    # 命令历史建议（按 → 接受）
  zsh-syntax-highlighting  # 命令语法高亮（有效命令绿色，无效红色）
  zsh-z                  # 智能目录跳转（基于 frecency 算法）
)

source $ZSH/oh-my-zsh.sh
```

---

## Starship 提示符

### 为什么选择 Starship？

| 特性 | [[Starship]] | Spaceship |
|------|----------|-----------|
| 启动速度 | ⚡ 极快（Rust 编写） | 🐢 较慢 |
| 跨 Shell | ✅ 支持（Zsh/Bash/Fish） | ❌ 仅 Zsh |
| 多语言版本 | ✅ 自动检测 | ✅ 支持 |
| 配置复杂度 | 简单（TOML） | 中等 |

### 安装

```bash
brew install starship
```

### 初始化

```bash
# ~/.zshrc 第 125 行

eval "$(starship init zsh)"
```

### 配置文件

**位置**：`~/.config/starship.toml`

```toml
# ~/.config/starship.toml 完整配置

# 插入空行
add_newline = true

# Prompt 格式定义
format = """
$username\
$hostname\
$directory\
$git_branch\
$git_status\
$git_state\
$nodejs\
$java\
$python\
$docker_context\
$line_break\
$character"""

# 字符提示符配置
[character]
success_symbol = "[❯](bold green)"  # 命令成功
error_symbol = "[❯](bold red)"      # 命令失败

# 目录配置
[directory]
truncation_length = 3              # 截断深度
truncation_symbol = "…/"           # 截断符号
style = "bold cyan"                # 颜色

# Git 分支配置
[git_branch]
symbol = " "                    # 分支图标
style = "bold purple"              # 颜色
truncation_length = 24             # 分支名截断长度

# Git 状态配置
[git_status]
style = "bold red"
modified = " [~](bold yellow)"     # 已修改
staged = "[+](bold green)"         # 已暂存
untracked = "[?](bold blue)"       # 未跟踪
deleted = "[-](bold red)"          # 已删除
ahead = "[⇡${count}](bold green)"  # 领先远程
behind = "[⇣${count}](bold red)"   # 落后远程

# Node.js 配置
[nodejs]
symbol = " "
style = "bold green"
detect_extensions = ["js", "mjs", "cjs", "ts", "mts", "cts"]
detect_files = ["package.json", ".node-version"]

# Java 配置
[java]
symbol = " "
style = "bold red"
detect_extensions = ["java", "class", "jar", "kt", "kts"]
detect_files = ["pom.xml", "build.gradle", "build.gradle.kts"]

# Python 配置
[python]
symbol = " "
style = "bold yellow"
detect_extensions = ["py"]
detect_files = ["requirements.txt", "pyproject.toml"]

# Docker 配置
[docker_context]
symbol = " "
style = "bold blue"
detect_extensions = ["Dockerfile", "docker-compose.yml"]

# 命令执行时间
[cmd_duration]
min_time = 2_000                   # 超过 2 秒才显示
style = "bold yellow"
format = "took [$duration]($style) "
```

### 提示符显示效果

```
~/projects/i/AITarot  main ✓  node v18.0.0  java 17  ❯
```

- 📁 当前目录（截断显示）
- 🌿 Git 分支和状态
- 📦 Node.js 版本（在 JS 项目中自动显示）
- ☕ Java 版本（在 Java 项目中自动显示）
- ❯ 命令提示符

### 符号说明

| 符号 | 含义 | 配置位置 |
|------|------|----------|
| `❯` | 命令执行成功 | `[character].success_symbol` |
| `❯` (红色) | 命令执行失败 | `[character].error_symbol` |
| `⇡N` | 领先远程 N 个提交 | `[git_status].ahead` |
| `⇣N` | 落后远程 N 个提交 | `[git_status].behind` |
| `~` | 文件已修改 | `[git_status].modified` |
| `+` | 文件已暂存 | `[git_status].staged` |
| `?` | 未跟踪文件 | `[git_status].untracked` |

---

## 常用别名

### 目录导航

```bash
# ~/.zshrc 第 72-85 行

alias projects="cd ~/projects"
alias i="cd ~/projects/i"        # 个人项目
alias r="cd ~/projects/r"        # 学习/复现项目
alias dl="cd ~/Downloads"
alias doc="cd ~/Documents"
alias ..="cd .."
alias ...="cd ../.."
alias ....="cd ../../.."
```

### 文件列表

```bash
# ~/.zshrc 第 148-151 行

alias ll="ls -la"    # 详细列表（含隐藏文件）
alias la="ls -A"     # 列出所有文件
alias l="ls -CF"     # 按列显示
```

### Git 快捷命令

```bash
# ~/.zshrc 第 153-160 行

alias gs="git status"
alias ga="git add"
alias gc="git commit"
alias gp="git push"
alias gl="git pull"
alias gd="git diff"
alias glog="git log --oneline --graph --decorate"
```

### Maven 快捷命令

```bash
# ~/.zshrc 第 162-166 行

alias mvn-clean="mvn clean"
alias mvn-build="mvn clean package -DskipTests"
alias mvn-test="mvn test"
alias mvn-run="mvn spring-boot:run"
```

### npm 快捷命令

```bash
# ~/.zshrc 第 168-173 行

alias ni="npm install"
alias nid="npm install --save-dev"
alias nr="npm run"
alias ns="npm start"
alias nt="npm test"
```

### Docker 快捷命令

```bash
# ~/.zshrc 第 175-181 行

alias dps="docker ps"
alias dpsa="docker ps -a"
alias dc="docker compose"
alias dcup="docker compose up -d"
alias dcdown="docker compose down"
alias dclogs="docker compose logs -f"
```

### 安全命令

```bash
# ~/.zshrc 第 134 行

alias rm="trash"  # 用 trash 替代 rm，移到废纸篓（可恢复）
```

---

## 实用函数

### mkcd - 创建并进入目录

```bash
# ~/.zshrc 第 187-189 行

function mkcd() {
  mkdir -p "$1" && cd "$1"
}
```

**使用示例**：
```bash
mkcd my-project
# 等同于：mkdir -p my-project && cd my-project
```

### ff - 查找文件

```bash
# ~/.zshrc 第 191-193 行

function ff() {
  find . -name "*$1*" -type f
}
```

**使用示例**：
```bash
ff "config"
# 查找当前目录下所有包含 "config" 的文件
```

### fd - 查找目录

```bash
# ~/.zshrc 第 195-197 行

function fd() {
  find . -name "*$1*" -type d
}
```

### port - 查看端口占用

```bash
# ~/.zshrc 第 199-201 行

function port() {
  lsof -i :"$1"
}
```

**使用示例**：
```bash
port 8080
# 查看 8080 端口被哪个进程占用
```

### serve - 启动 HTTP 服务器

```bash
# ~/.zshrc 第 203-206 行

function serve() {
  local port="${1:-8000}"
  python3 -m http.server "$port"
}
```

**使用示例**：
```bash
serve       # 在当前目录启动 HTTP 服务器（端口 8000）
serve 3000  # 指定端口 3000
```

### gac - Git 快速提交

```bash
# ~/.zshrc 第 208-210 行

function gac() {
  git add -A && git commit -m "$1"
}
```

**使用示例**：
```bash
gac "feat: add new feature"
# 等同于：git add -A && git commit -m "feat: add new feature"
```

### gacp - Git 快速提交并推送

```bash
# ~/.zshrc 第 212-214 行

function gacp() {
  git add -A && git commit -m "$1" && git push
}
```

---

## 插件说明

### git

[[Oh-My-Zsh]] 内置插件，提供：
- Git 命令自动补全
- Git 分支名显示
- 常用 Git 别名（如 `gst` = `git status`）

### zsh-autosuggestions

根据命令历史提供自动建议。

**使用方法**：
- 输入命令时，会显示灰色建议
- 按 `→` 或 `End` 接受建议
- 按 `Ctrl+F` 接受下一个单词

**安装**：
```bash
git clone https://github.com/zsh-users/zsh-autosuggestions \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

### zsh-syntax-highlighting

为命令提供语法高亮。

**效果**：
- 有效命令显示为绿色
- 无效命令显示为红色
- 字符串显示为黄色

**安装**：
```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

### zsh-z

智能目录跳转，基于 frecency（频率 + 最近使用）算法。

**使用方法**：
```bash
z projects    # 跳转到最近访问的包含 "projects" 的目录
z ai          # 跳转到最近访问的包含 "ai" 的目录
```

**安装**：
```bash
git clone https://github.com/agkozak/zsh-z $ZSH_CUSTOM/plugins/zsh-z
```

---

## 修改理由

### 1. 从 Spaceship 切换到 Starship

**问题**：
- Spaceship 是 [[Oh-My-Zsh]] 主题，启动速度较慢
- 与 Oh My Zsh 耦合，不够灵活

**解决方案**：
- 使用 [[Starship]]，它是独立的提示符工具
- 启动速度更快（Rust 编写）
- 跨 Shell 兼容（Zsh/Bash/Fish）

**源码变更**：
```bash
# 旧配置（已移除）
ZSH_THEME="spaceship"

# 新配置
ZSH_THEME=""  # 第 14 行
eval "$(starship init zsh)"  # 第 125 行
```

### 2. 清理 .zshrc 注释

**问题**：
- 原配置文件有大量注释（70+ 行），难以阅读
- 很多未使用的配置选项

**解决方案**：
- 移除所有未使用的注释
- 保留必要的配置和说明
- 按功能分组，便于维护

### 3. 添加实用别名和函数

**问题**：
- 原配置缺少常用命令的快捷方式
- 重复输入长命令效率低

**解决方案**：
- 为 Git、Maven、npm、Docker 添加别名
- 创建常用函数（mkcd、ff、port 等）
- 按功能分组，便于记忆

### 4. 配置环境变量

**问题**：
- 原配置缺少开发工具的 PATH 配置
- 语言环境未设置

**解决方案**：
```bash
# 添加的环境变量
export LANG=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8
export EDITOR='vim'
export VISUAL='vim'
export PATH="$HOME/.local/bin:$PATH"
export PATH="/opt/homebrew/bin:$PATH"
export M2_HOME="/opt/homebrew/opt/maven"
export PATH="$M2_HOME/bin:$PATH"
```

### 5. 使用 trash 替代 rm

**问题**：
- `rm` 命令删除文件后无法恢复
- 容易误删重要文件

**解决方案**：
```bash
alias rm="trash"  # 移到废纸篓，可恢复
```

---

## 常见问题

### Q: 如何让配置生效？

```bash
source ~/.zshrc
```

或者重新打开终端。

### Q: 如何查看所有别名？

```bash
alias
```

### Q: 如何临时禁用别名？

```bash
\rm file.txt       # 使用原始 rm 命令
command rm file.txt # 另一种方式
```

### Q: 如何添加自己的本地配置？

创建 `~/.zshrc.local` 文件，它会在 `~/.zshrc` 末尾自动加载：

```bash
# ~/.zshrc 末尾
[ -f ~/.zshrc.local ] && source ~/.zshrc.local
```

### Q: Starship 提示符不显示怎么办？

```bash
# 检查是否安装
starship --version

# 检查是否初始化
grep "starship init" ~/.zshrc

# 重新安装
brew install starship
```

### Q: 插件不生效怎么办？

```bash
# 检查插件是否安装
ls ~/.oh-my-zsh/custom/plugins/

# 重新克隆插件
git clone https://github.com/zsh-users/zsh-autosuggestions \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

---

## 相关页面

- [[Starship]] — 提示符配置详情
- [[Oh-My-Zsh]] — Zsh 框架配置
- [[gitConfig]] — Git 配置指南
- [[Git]] — Git 基础命令

## 外部资源

- [Oh My Zsh 官方文档](https://ohmyz.sh/)
- [Starship 官方文档](https://starship.rs/zh-CN/)
- [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)
- [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)
