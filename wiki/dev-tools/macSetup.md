---
type: topic
tags:
  - mac
  - 配置
  - 还原
  - 环境搭建
article_id: OBA-DEVTOOLS006
created_at: 2026-06-03
updated_at: 2026-06-03
---

# Mac 配置还原指南

> 本文档记录 Flora 的 Mac 完整配置，用于快速还原一台相同配置的电脑。
> 最后更新：2026-06-03

## 📋 目录

- [系统信息](#系统信息)
- [基础环境](#基础环境)
- [开发工具](#开发工具)
- [效率工具](#效率工具)
- [终端配置](#终端配置)
- [编辑器配置](#编辑器配置)
- [Dotfiles](#dotfiles)
- [系统设置](#系统设置)
- [还原步骤](#还原步骤)

---

## 系统信息

| 项目 | 版本 |
|------|------|
| macOS | 26.5 (Sequoia) |
| 芯片 | Apple Silicon (arm64) |
| Homebrew | 5.1.14 |

---

## 基础环境

### 1. 安装 Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 2. 安装基础工具

```bash
# 核心工具
brew install git
brew install node
brew install python@3.13
brew install openjdk
brew install maven
brew install redis
brew install starship
brew install trash
brew install yt-dlp
brew install cc-switch
```

### 3. 安装 Oh My Zsh

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### 4. 安装 Zsh 插件

```bash
# zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# z-z (快速跳转)
git clone https://github.com/agkozak/zsh-z ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-z
```

---

## 开发工具

### 版本信息

| 工具 | 版本 |
|------|------|
| Node.js | v23.11.0 |
| Java | OpenJDK 21.0.11 (Temurin) |
| Python | 3.13.3 |
| Maven | 3.9.9 |
| Redis | 8.0.1 |
| Git | 2.49.0 |
| Starship | 1.23.0 |
| Docker | 29.4.1 |

### 必装应用

```bash
# IDE 和编辑器
brew install --cask visual-studio-code
brew install --cask cursor
brew install --cask obsidian

# 数据库工具
brew install --cask docker

# API 测试
# Postman 或其他工具
```

---

## 效率工具

### 必装应用列表

| 应用 | 用途 | 安装方式 |
|------|------|----------|
| **Alfred 5** | 效率启动器 | brew install --cask alfred |
| **Rectangle** | 窗口管理 | brew install --cask rectangle |
| **Thor Launcher** | 快速启动 | brew install --cask thor |
| **Bob** | 翻译工具 | brew install --cask bob |
| **Keka** | 压缩工具 | brew install --cask keka |
| **Ghostty** | 终端 | brew install --cask ghostty |
| **Karabiner-Elements** | 键盘映射 | brew install --cask karabiner-elements |
| **IINA** | 视频播放 | brew install --cask iina |
| **RunCat** | CPU 监控 | brew install --cask runcat |
| **Downie 4** | 视频下载 | brew install --cask downie |

### 社交和办公

| 应用 | 用途 | 安装方式 |
|------|------|----------|
| **微信** | 社交 | brew install --cask wechat |
| **QQ** | 社交 | brew install --cask qq |
| **飞书** | 办公 | brew install --cask lark |
| **腾讯会议** | 会议 | brew install --cask tencent-meeting |
| **百度网盘** | 云存储 | brew install --cask baidu-netdisk |
| **迅雷** | 下载 | brew install --cask thunder |

### 开发相关

| 应用 | 用途 | 安装方式 |
|------|------|----------|
| **ChatGPT** | AI 助手 | brew install --cask chatgpt |
| **Gemini** | AI 助手 | brew install --cask gemini |
| **Claude** | AI 助手 | brew install --cask claude |
| **Typora** | Markdown | brew install --cask typora |
| **Google Chrome** | 浏览器 | brew install --cask google-chrome |
| **Clash** | 代理 | brew install --cask clash |

### 一键安装脚本

```bash
# 效率工具
brew install --cask alfred rectangle thor bob keka ghostty karabiner-elements iina runcat downie

# 社交办公
brew install --cask wechat qq lark tencent-meeting baidu-netdisk thunder

# 开发相关
brew install --cask chatgpt gemini claude typora google-chrome clash

# Microsoft Office
brew install --cask microsoft-word microsoft-excel microsoft-powerpoint
```

---

## 终端配置

### Ghostty 配置

**配置文件**：`~/.config/ghostty/config.ghostty`

```conf
# 主题
theme = Kanagawa Dragon

# 字体
font-family = JetBrains Mono
font-size = 14

# 窗口
window-padding-x = 8
window-padding-y = 4
background-opacity = 0.95

# 快捷键
keybind = ctrl+shift+t=new_tab
keybind = ctrl+shift+w=close_surface
keybind = ctrl+tab=next_tab
keybind = ctrl+shift+tab=previous_tab
```

### Starship 配置

**配置文件**：`~/.config/starship.toml`

```toml
[character]
success_symbol = "[❯](bold green)"
error_symbol = "[❯](bold red)"

[git_branch]
symbol = " "
style = "bold purple"

[git_status]
modified = " [!](bold yellow)"

[nodejs]
symbol = " "

[java]
symbol = " "

[python]
symbol = " "

[docker_context]
symbol = " "
```

### Zsh 配置

**配置文件**：`~/.zshrc`

```bash
# Oh My Zsh 路径
export ZSH="$HOME/.oh-my-zsh"

# 主题（使用 Starship，所以设为空）
ZSH_THEME=""

# 插件
plugins=(git zsh-autosuggestions zsh-syntax-highlighting zsh-z)

# 加载 Oh My Zsh
source $ZSH/oh-my-zsh.sh

# 别名
alias rm="trash"
alias gs="git status"
alias ga="git add ."
alias gc="git commit -m"
alias gp="git push"
alias gpl="git pull"
alias gl="git log --oneline --graph"
alias gd="git diff"
alias gco="git checkout"
alias gb="git branch"
alias gba="git branch -a"
alias gbd="git branch -d"
alias gbm="git branch -M"
alias gfa="git fetch --all"
alias grh="git reset --hard"
alias gcp="git cherry-pick"
alias gst="git stash"
alias gstp="git stash pop"

# Maven 别名
alias mi="mvn clean install"
alias mc="mvn clean compile"
alias mt="mvn test"
alias mp="mvn package"

# Docker 别名
alias dps="docker ps"
alias dpsa="docker ps -a"
alias di="docker images"
alias dc="docker compose"
alias dcu="docker compose up -d"
alias dcd="docker compose down"
alias dcl="docker compose logs -f"

# 函数
mkcd() { mkdir -p "$1" && cd "$1"; }
ff() { find . -name "*$1*" -type f; }
fd() { find . -name "*$1*" -type d; }
port() { lsof -i :$1; }
serve() { python3 -m http.server $1; }

# Starship 初始化
eval "$(starship init zsh)"
```

---

## 编辑器配置

### VS Code

**版本**：1.121.0

**必装扩展**：

```bash
# AI 助手
code --install-extension anthropic.claude-code

# 语言支持
code --install-extension vue.volar
code --install-extension redhat.java
code --install-extension ms-python.python
code --install-extension golang.go

# 代码质量
code --install-extension dbaeumer.vscode-eslint
code --install-extension esbenp.prettier-vscode
code --install-extension davidanson.vscode-markdownlint

# 工具
code --install-extension ms-vscode-remote.vscode-remote-extensionpack
code --install-extension ms-azuretools.vscode-docker
code --install-extension cweijan.vscode-mysql-client2
code --install-extension shd101wyy.markdown-preview-enhanced

# 主题
code --install-extension teabyii.ayu
code --install-extension github.github-vscode-theme
```

**设置文件**：`~/Library/Application Support/Code/User/settings.json`

```json
{
  "workbench.colorTheme": "Ayu Mirage Bordered",
  "workbench.iconTheme": "ayu",
  "editor.suggest.snippetsPreventQuickSuggestions": false,
  "explorer.confirmDelete": false,
  "code-runner.runInTerminal": true,
  "security.workspace.trust.untrustedFiles": "open",
  "redhat.telemetry.enabled": true
}
```

### Cursor

**版本**：3.2.16

Cursor 配置与 VS Code 类似，可同步 VS Code 扩展。

---

## Dotfiles

### 配置文件位置

所有 dotfiles 备份在 `~/Documents/SoftwareConfiguration/dotfiles/`

```
~/Documents/SoftwareConfiguration/dotfiles/
├── .gitconfig          # Git 配置
├── .gitignore_global   # Git 全局忽略规则
├── .zshrc              # Zsh 配置
├── .vimrc              # Vim 配置
├── .ideavimrc          # IntelliJ Vim 配置
└── README.md           # 说明文档
```

### Git 配置

**文件**：`~/.gitconfig`

```gitconfig
[user]
    name = Flora
    email = alexye0602@gmail.com

[push]
    default = current
    autoSetupRemote = true

[url "git@git.com"]
    insteadOf = https://github.com

[core]
    autocrlf = input
    editor = vim

[init]
    defaultBranch = main

[merge]
    conflictstyle = diff3

[pull]
    rebase = true

[rerere]
    enabled = true

[alias]
    st = status -sb
    lg = log --oneline --graph --decorate --all
    co = checkout
    cob = checkout -b
    br = branch
    bra = branch -a
    ci = commit
    ca = commit --amend
    cp = cherry-pick
    df = diff
    dfs = diff --staged
    rb = rebase
    rbi = rebase -i
    mg = merge
    rs = reset
    rsh = reset --hard
    rsl = reset HEAD~
    sl = stash list
    sa = stash apply
    sp = stash pop
    ss = stash save
    fa = fetch --all
    ps = push
    psf = push --force-with-lease
    pl = pull
    psu = push -u origin HEAD
```

### Git 全局忽略

**文件**：`~/.gitignore_global`

```gitignore
# macOS
.DS_Store
.AppleDouble
.LSOverride
._*

# IDE
.idea/
.vscode/
*.swp
*.swo
*~

# Java
*.class
*.jar
*.war
*.ear
target/
build/

# Node
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Python
__pycache__/
*.py[cod]
*$py.class
.env
.venv/

# 环境变量
.env
.env.local
.env.*.local

# 日志
*.log
logs/

# 编译产物
dist/
out/
bin/
```

---

## 系统设置

### 键盘映射 (Karabiner)

**配置文件**：`~/.config/karabiner/karabiner.json`

```json
{
    "profiles": [
        {
            "complex_modifications": {
                "rules": [
                    {
                        "description": "Change right_command key to command+control+option+shift. (Post f19 key when pressed alone)",
                        "manipulators": [
                            {
                                "from": {
                                    "key_code": "right_command",
                                    "modifiers": { "optional": ["any"] }
                                },
                                "to": [
                                    {
                                        "key_code": "left_shift",
                                        "modifiers": ["left_command", "left_control", "left_option"]
                                    }
                                ],
                                "to_if_alone": [{ "key_code": "f19" }],
                                "type": "basic"
                            }
                        ]
                    }
                ]
            },
            "name": "Default profile",
            "selected": true,
            "virtual_hid_keyboard": { "keyboard_type_v2": "ansi" }
        }
    ]
}
```

**说明**：
- `Right Command` 单独按下 → `F19`（触发 Alfred）
- `Right Command` + 其他键 → `⌘ + ⌃ + ⌥ + ⇧`（超级快捷键）

### Alfred 配置

**热键**：`F19`（通过 Karabiner 映射）

**基本配置**：
- 启动快捷键：F19
- 剪贴板历史：已启用
- 片段：待配置
- 工作流：待配置

### Rectangle 配置

**窗口管理快捷键**：

| 快捷键 | 功能 |
|--------|------|
| `⌃ + ⌥ + ←` | 左半屏 |
| `⌃ + ⌥ + →` | 右半屏 |
| `⌃ + ⌥ + ↑` | 上半屏 |
| `⌃ + ⌥ + ↓` | 下半屏 |
| `⌃ + ⌥ + ⏎` | 全屏 |
| `⌃ + ⌥ + C` | 居中 |

### 系统偏好设置

**外观**：
- 外观：深色模式
- 强调色：默认（蓝色）

**Dock**：
- 自动隐藏：是
- 图标大小：中等

**键盘**：
- Key Repeat：最快
- Delay Until Repeat：最短

---

## 还原步骤

### 第一步：基础环境

```bash
# 1. 安装 Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 2. 安装基础工具
brew install git node python@3.13 openjdk maven redis starship trash yt-dlp cc-switch

# 3. 安装 Oh My Zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 4. 安装 Zsh 插件
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/agkozak/zsh-z ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-z
```

### 第二步：安装应用

```bash
# 效率工具
brew install --cask alfred rectangle thor bob keka ghostty karabiner-elements iina runcat downie

# 社交办公
brew install --cask wechat qq lark tencent-meeting baidu-netdisk thunder

# 开发相关
brew install --cask visual-studio-code cursor obsidian docker chatgpt gemini claude typora google-chrome clash

# Microsoft Office
brew install --cask microsoft-word microsoft-excel microsoft-powerpoint
```

### 第三步：恢复配置

```bash
# 1. 克隆 dotfiles
cd ~/Documents/SoftwareConfiguration
git clone <your-dotfiles-repo> dotfiles

# 2. 创建符号链接
ln -sf ~/Documents/SoftwareConfiguration/dotfiles/.zshrc ~/.zshrc
ln -sf ~/Documents/SoftwareConfiguration/dotfiles/.gitconfig ~/.gitconfig
ln -sf ~/Documents/SoftwareConfiguration/dotfiles/.gitignore_global ~/.gitignore_global
ln -sf ~/Documents/SoftwareConfiguration/dotfiles/.vimrc ~/.vimrc
ln -sf ~/Documents/SoftwareConfiguration/dotfiles/.ideavimrc ~/.ideavimrc

# 3. 恢复 Ghostty 配置
mkdir -p ~/.config/ghostty
cp ~/Documents/SoftwareConfiguration/dotfiles/ghostty/config.ghostty ~/.config/ghostty/

# 4. 恢复 Starship 配置
cp ~/Documents/SoftwareConfiguration/dotfiles/starship.toml ~/.config/starship.toml

# 5. 恢复 Karabiner 配置
mkdir -p ~/.config/karabiner
cp ~/Documents/SoftwareConfiguration/dotfiles/karabiner.json ~/.config/karabiner/
```

### 第四步：配置应用

1. **Alfred**：设置热键为 F19，启用剪贴板历史
2. **Rectangle**：配置窗口管理快捷键
3. **Thor**：配置快速启动应用
4. **VS Code**：安装扩展，同步设置
5. **Ghostty**：应用 Kanagawa Dragon 主题
6. **Karabiner**：导入键盘映射配置

### 第五步：验证配置

```bash
# 验证 Zsh 配置
source ~/.zshrc
starship --version

# 验证 Git 配置
git config --list

# 验证开发工具
java -version
node --version
python3 --version
mvn --version
```

---

## 项目目录结构

```
~/Projects/
├── yu-ai-code-mother/     # AI 代码生成平台
├── yu-picture/            # 图片管理平台
├── mars-admin/            # 企业级后台管理系统
├── AITarot/               # AI 塔罗牌项目
└── FloraPic/              # 图片处理项目
```

---

## Obsidian 配置

**Vault 位置**：`~/Library/Mobile Documents/com~apple~CloudDocs/FloNote/`

**插件**：
- Git（自动同步）
- Claude Code（AI 辅助）
- 其他常用插件

**同步**：通过 iCloud 自动同步

---

## 备份策略

### Dotfiles 备份

```bash
# 定期备份到 Git
cd ~/Documents/SoftwareConfiguration/dotfiles
git add .
git commit -m "Update dotfiles"
git push
```

### 应用数据备份

- **Alfred**：`~/Library/Application Support/Alfred/`
- **VS Code**：`~/Library/Application Support/Code/`
- **Ghostty**：`~/.config/ghostty/`
- **Karabiner**：`~/.config/karabiner/`

---

## 相关文档

- [[zshAndShell]] — Zsh 配置详解
- [[gitConfig]] — Git 配置详解
- [[ghostty]] — Ghostty 配置详解
- [[wiki/dev-tools/README]] — 开发工具总览

---

## 更新日志

| 日期 | 操作 | 说明 |
|------|------|------|
| 2026-06-03 | 新建 | 创建 Mac 配置还原指南 |
