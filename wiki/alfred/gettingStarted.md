---
type: topic
tags:
  - alfred
  - 入门
  - 配置
article_id: OBA-ALFRED002
created_at: 2026-06-03
updated_at: 2026-06-03
---

# Alfred 入门指南

> 本文档介绍 Alfred 的基础配置和入门使用方法。

## 📦 安装 Alfred

### 下载安装

1. 访问 [Alfred 官网](https://www.alfredapp.com/)
2. 下载 Alfred 4 或 Alfred 5
3. 拖拽到 Applications 文件夹
4. 首次启动需要授权辅助功能权限

### Powerpack（可选）

Alfred 基础版免费，Powerpack 付费版解锁以下功能：
- 工作流（Workflow）
- 片段（Snippets）
- 剪贴板历史（Clipboard History）
- 主题自定义

## ⚙️ 基础配置

### 1. 设置启动快捷键

**当前位置**：`Alfred Preferences → General → Alfred Hotkey`

**当前配置**：`F19`

**推荐配置**：
- `⌘ + Space`（与 Spotlight 冲突，需先禁用 Spotlight）
- `⌘ + ⌥ + Space`（推荐，无冲突）
- `F19`（当前配置，需要键盘支持）

### 2. 权限设置

首次使用需要授权以下权限：

```
系统偏好设置 → 隐私与安全性 → 辅助功能
✅ 勾选 Alfred
```

### 3. 设置默认搜索

```
Alfred Preferences → Features → Default Results
```

可配置：
- 应用程序搜索
- 文件搜索
- 书签搜索
- 联系人搜索

## 🎯 基础使用

### 启动应用

```
F19 → 输入应用名 → Enter
```

**示例**：
```
F19 → chrome → Enter  （打开 Chrome）
F19 → vscode → Enter  （打开 VS Code）
F19 → ghostty → Enter （打开 Ghostty）
F19 → 微信 → Enter    （打开微信）
```

### 文件搜索

```
F19 → find 文件名 → Enter
F19 → open 文件名 → Enter
```

### 计算器

直接输入数学表达式：
```
F19 → 123 * 456 → Enter
F19 → sqrt(144) → Enter
F19 → 100 USD to CNY → Enter
```

### 系统命令

```
F19 → lock → Enter      （锁屏）
F19 → sleep → Enter     （休眠）
F19 → emptytrash → Enter（清空废纸篓）
F19 → quitall → Enter   （退出所有应用）
```

## 📂 配置文件位置

Alfred 的配置存储在以下位置：

```
~/Library/Application Support/Alfred/
├── Alfred.alfredpreferences/    # 偏好设置
├── Databases/                   # 数据库
│   ├── clipboard.alfdb         # 剪贴板历史
│   ├── snippets.alfdb          # 片段
│   └── bookmarks.alfdb         # 书签
├── Workflow Data/               # 工作流数据
└── prefs.json                   # 主配置
```

## 🔗 相关文档

- [[basicFeatures]] — 核心功能详解
- [[shortcuts]] — 快捷键大全
- [[workflows]] — 工作流开发指南
