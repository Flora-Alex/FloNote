---
type: topic
tags: [dev-tools, terminal, ghostty, 配置, 快捷键]
article_id: OBA-DEVTOOLS005
created_at: 2026/06/02
updated_at: 2026/06/02
---

# Ghostty 终端配置指南

> 配置文件位置：`~/.config/ghostty/config.ghostty`
> 重新加载：`Cmd+Shift+,` (macOS)
> 查看默认配置：`ghostty +show-config --default --docs`

---

## 📋 目录

- [概述](#概述)
- [配置详解](#配置详解)
- [快捷键速查](#快捷键速查)
- [主题配色](#主题配色)
- [优化记录](#优化记录)
- [常见问题](#常见问题)

---

## 概述

[[Ghostty]] 是一个现代化、高性能的终端模拟器，使用 Zig 编写，特点是：

- ⚡ 极快的渲染速度
- 🎨 高度可定制
- 🔒 安全特性完善
- 📱 原生 macOS 体验

### 配置文件位置

```
~/.config/ghostty/config.ghostty    # 主配置文件
~/.config/ghostty/shaders/          # 自定义 shader 目录（可选）
```

---

## 配置详解

### Shader（着色器）

```ghostty
# ~/.config/ghostty/config.ghostty 第 9-12 行

# 注意：需要先下载 shader 文件到 ~/.config/ghostty/shaders/
# 如果没有 shader 文件，请注释掉以下两行
# custom-shader = shaders/cursor_sweep.glsl
# custom-shader = shaders/cursor_warp.glsl
```

**说明**：自定义光标动画效果。需要额外下载 shader 文件，否则会报错。

**下载 shader**：
```bash
mkdir -p ~/.config/ghostty/shaders
# 从 GitHub 下载 shader 文件
curl -o ~/.config/ghostty/shaders/cursor_sweep.glsl <url>
curl -o ~/.config/ghostty/shaders/cursor_warp.glsl <url>
```

### Platform（平台）

```ghostty
# ~/.config/ghostty/config.ghostty 第 14 行

macos-option-as-alt = true
```

**说明**：将 macOS 的 Option 键映射为 Alt 键，方便在终端中使用 Alt 快捷键（如 Alt+B/F 跳转单词）。

### Typography（字体）

```ghostty
# ~/.config/ghostty/config.ghostty 第 17-21 行

font-family = "Liga Comic Mono"
# 中文字体回退（macOS 内置）
font-family = "PingFang SC"
font-size = 14
# font-thicken = true  # Retina 显示器可考虑启用
adjust-cell-height = 4
```

**配置说明**：

| 选项 | 值 | 说明 |
|------|-----|------|
| `font-family` | Liga Comic Mono | 主字体（等宽，带连字） |
| `font-family` | PingFang SC | 中文回退字体 |
| `font-size` | 14 | 字体大小 |
| `font-thicken` | false | 字体加粗（Retina 可启用） |
| `adjust-cell-height` | 4 | 单元格高度调整（像素） |

**推荐编程字体**：
- JetBrains Mono
- Fira Code
- Cascadia Code
- Liga Comic Mono（当前使用）

### Window & Layout（窗口布局）

```ghostty
# ~/.config/ghostty/config.ghostty 第 24-31 行

window-save-state = always
unfocused-split-opacity = 0.4
# background-opacity = 0.85
# background-blur-radius = 30
# macos-titlebar-style = transparent
# window-padding-x = 10
# window-padding-y = 8
# window-theme = auto
```

**配置说明**：

| 选项 | 值 | 说明 |
|------|-----|------|
| `window-save-state` | always | 保存窗口状态（位置、大小） |
| `unfocused-split-opacity` | 0.4 | 非焦点分屏的透明度 |
| `background-opacity` | 0.85 | 背景透明度（注释掉） |
| `background-blur-radius` | 30 | 背景模糊半径（注释掉） |

### Cursor（光标）

```ghostty
# ~/.config/ghostty/config.ghostty 第 34-36 行

cursor-style = block
cursor-style-blink = true
cursor-opacity = 0.8
```

**光标样式选项**：
- `block` — 方块光标（当前）
- `bar` — 竖线光标
- `underline` — 下划线光标

### Mouse & Selection（鼠标选择）

```ghostty
# ~/.config/ghostty/config.ghostty 第 39-40 行

mouse-hide-while-typing = true
copy-on-select = clipboard
```

**说明**：
- 输入时自动隐藏鼠标
- 选中文本自动复制到剪贴板

### Quick Terminal（快速终端）

```ghostty
# ~/.config/ghostty/config.ghostty 第 43-46 行

quick-terminal-position = top
quick-terminal-screen = mouse
quick-terminal-autohide = true
quick-terminal-animation-duration = 0.15
```

**说明**：Quake 风格的下拉终端，通过 `Option+Space` 全局快捷键触发。

| 选项 | 值 | 说明 |
|------|-----|------|
| `quick-terminal-position` | top | 从顶部滑出 |
| `quick-terminal-screen` | mouse | 在鼠标所在的屏幕显示 |
| `quick-terminal-autohide` | true | 失焦后自动隐藏 |
| `quick-terminal-animation-duration` | 0.15 | 动画时长（秒） |

### Security（安全）

```ghostty
# ~/.config/ghostty/config.ghostty 第 49-50 行

clipboard-paste-protection = true
clipboard-paste-bracketed-safe = true
```

**说明**：
- 剪贴板粘贴保护（防止意外粘贴危险命令）
- 括号粘贴安全模式（防止粘贴时自动执行）

### Shell Integration（Shell 集成）

```ghostty
# ~/.config/ghostty/config.ghostty 第 53 行

shell-integration = zsh
```

**说明**：启用与 Zsh 的深度集成，支持：
- 命令追踪
- 目录标记
- 提示符检测

### Performance（性能）

```ghostty
# ~/.config/ghostty/config.ghostty 第 56-57 行

# 滚动缓冲区：10000 行足够日常使用（约 1-2MB）
scrollback-limit = 10000
```

**优化说明**：
- 原配置：25MB（25000000）— 过大，浪费内存
- 优化后：10000 行 — 足够日常使用

**内存占用估算**：
- 10000 行 × 100 字符 × 4 字节 ≈ 4MB
- 25000000 行 × 100 字符 × 4 字节 ≈ 10GB

---

## 快捷键速查

### 标签页操作

| 快捷键 | 功能 | 配置源码 |
|--------|------|----------|
| `Cmd+T` | 新建标签页 | `keybind = cmd+t=new_tab` |
| `Cmd+Shift+←` | 上一个标签页 | `keybind = cmd+shift+left=previous_tab` |
| `Cmd+Shift+→` | 下一个标签页 | `keybind = cmd+shift+right=next_tab` |
| `Cmd+W` | 关闭当前分屏 | `keybind = cmd+w=close_surface` |

### 分屏操作

| 快捷键 | 功能 | 配置源码 |
|--------|------|----------|
| `Cmd+D` | 向右分屏 | `keybind = cmd+d=new_split:right` |
| `Cmd+Shift+D` | 向下分屏 | `keybind = cmd+shift+d=new_split:down` |
| `Cmd+Alt+←` | 切换到左边分屏 | `keybind = cmd+alt+left=goto_split:left` |
| `Cmd+Alt+→` | 切换到右边分屏 | `keybind = cmd+alt+right=goto_split:right` |
| `Cmd+Alt+↑` | 切换到上方分屏 | `keybind = cmd+alt+up=goto_split:top` |
| `Cmd+Alt+↓` | 切换到下方分屏 | `keybind = cmd+alt+down=goto_split:bottom` |
| `Cmd+Shift+E` | 均分所有分屏 | `keybind = cmd+shift+e=equalize_splits` |
| `Cmd+Shift+F` | 切换分屏最大化 | `keybind = cmd+shift+f=toggle_split_zoom` |

### 字体大小

| 快捷键 | 功能 | 配置源码 |
|--------|------|----------|
| `Cmd++` | 增大字体 | `keybind = cmd+plus=increase_font_size:1` |
| `Cmd+-` | 减小字体 | `keybind = cmd+minus=decrease_font_size:1` |
| `Cmd+0` | 重置字体大小 | `keybind = cmd+zero=reset_font_size` |

### 其他

| 快捷键 | 功能 | 配置源码 |
|--------|------|----------|
| `Cmd+Shift+,` | 重新加载配置 | `keybind = cmd+shift+comma=reload_config` |
| `Option+Space` | 全局快速终端 | `keybind = global:option+space=toggle_quick_terminal` |

---

## 主题配色

当前使用 **Kanagawa Dragon** 主题：

```ghostty
# ~/.config/ghostty/config.ghostty 第 85-107 行

background = #1f1f28        # 背景色（深灰）
foreground = #dcd7ba        # 前景色（米白）
selection-background = #2d4f67  # 选区背景（深蓝）
selection-foreground = #c8c093  # 选区前景（浅黄）
cursor-color = #dcd7ba      # 光标颜色
cursor-text = #1f1f28       # 光标文本颜色

# 16 色调色板
palette = 0=#16161d         # Black
palette = 1=#c34043         # Red
palette = 2=#76946a         # Green
palette = 3=#c0a36e         # Yellow
palette = 4=#7e9cd8         # Blue
palette = 5=#957fb8         # Magenta
palette = 6=#6a9589         # Cyan
palette = 7=#c8c093         # White
palette = 8=#727169         # Bright Black
palette = 9=#e82424         # Bright Red
palette = 10=#98bb6c        # Bright Green
palette = 11=#e6c384        # Bright Yellow
palette = 12=#7fb4ca        # Bright Blue
palette = 13=#938aa9        # Bright Magenta
palette = 14=#7aa89f        # Bright Cyan
palette = 15=#dcd7ba        # Bright White
```

**配色效果**：
- 深色背景，护眼
- 对比度适中
- 语法高亮效果好

---

## 优化记录

### 2026-06-02 优化内容

#### 1. 🔴 修复 Shader 文件缺失

**问题**：
- 配置引用了 `shaders/cursor_sweep.glsl` 和 `shaders/cursor_warp.glsl`
- 但 `~/.config/ghostty/shaders/` 目录不存在
- 会导致启动时加载错误

**解决方案**：
```ghostty
# 注释掉 shader 配置
# custom-shader = shaders/cursor_sweep.glsl
# custom-shader = shaders/cursor_warp.glsl
```

#### 2. 🟡 优化 scrollback-limit

**问题**：
- 原配置：`scrollback-limit = 25000000`（25MB）
- 会占用大量内存（可能 10GB+）

**解决方案**：
```ghostty
# 优化后：10000 行，约 4MB
scrollback-limit = 10000
```

**内存对比**：
| 配置 | 行数 | 预估内存 |
|------|------|----------|
| 原配置 | 25,000,000 | ~10GB |
| 优化后 | 10,000 | ~4MB |

#### 3. 🟡 添加中文字体回退

**问题**：
- 未配置中文字体，可能导致中文显示为方块

**解决方案**：
```ghostty
font-family = "Liga Comic Mono"
font-family = "PingFang SC"  # macOS 内置中文字体
```

#### 4. 🟢 明确 Shell 集成

**原配置**：
```ghostty
shell-integration = detect
```

**优化后**：
```ghostty
shell-integration = zsh
```

**说明**：明确指定 Zsh，避免检测开销。

---

## 常见问题

### Q: 如何重新加载配置？

```bash
# 方法 1：快捷键
Cmd+Shift+,

# 方法 2：命令行
killall -USR2 ghostty
```

### Q: 如何查看当前配置？

```bash
ghostty +show-config
```

### Q: 如何查看可用字体？

```bash
ghostty +list-fonts
```

### Q: Quick Terminal 不工作怎么办？

1. 检查全局快捷键是否冲突
2. 确认 `keybind = global:option+space=toggle_quick-terminal` 配置正确
3. 重启 Ghostty

### Q: 中文显示为方块怎么办？

添加中文字体回退：
```ghostty
font-family = "PingFang SC"
# 或
font-family = "Noto Sans CJK SC"
```

### Q: 如何启用背景透明？

```ghostty
background-opacity = 0.85
background-blur-radius = 30
```

**注意**：透明背景可能影响性能。

---

## 相关页面

- [[zshAndShell]] — Zsh 配置
- [[Starship]] — 提示符配置

## 外部资源

- [Ghostty 官方文档](https://ghostty.org/docs)
- [Ghostty GitHub](https://github.com/ghostty-org/ghostty)
- [Kanagawa 主题](https://github.com/rebelot/kanagawa.nvim)
