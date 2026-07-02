---
type: topic
tags:
  - alfred
  - 功能
  - 搜索
article_id: OBA-ALFRED003
created_at: 2026-06-03
updated_at: 2026-06-03
---

# Alfred 核心功能详解

> 本文档详细介绍 Alfred 的核心功能，包括搜索、剪贴板、片段等。

## 🔍 搜索功能

### 应用程序搜索

最常用的功能，快速启动应用：

```
F19 → 应用名 → Enter
```

**智能匹配**：
- 支持拼音首字母：`wx` → 微信
- 支持模糊匹配：`chr` → Chrome
- 支持中文：`微信` → 微信

### 文件搜索

```
F19 → find 文件名 → Enter
F19 → open 文件名 → Enter
```

**搜索范围**：
- 默认搜索用户目录
- 可配置搜索整个磁盘
- 支持文件类型过滤

### Web 搜索

```
F19 → 关键词 搜索内容 → Enter
```

**内置搜索**：
- `google 搜索内容` — Google 搜索
- `wiki 关键词` — Wikipedia
- `define 单词` — 词典查询

## 📋 剪贴板历史

### 启用剪贴板历史

```
Alfred Preferences → Features → Clipboard History
✅ 勾选 Enable Clipboard History
```

### 使用方法

```
F19 → ⌘ + ⌥ + C → 选择历史记录 → Enter
```

**功能特性**：
- 记录复制过的文本、图片、文件
- 支持搜索历史内容
- 可设置保存时间（24 小时 / 7 天 / 永久）
- 支持置顶常用内容

### 配置选项

```
Alfred Preferences → Features → Clipboard History
- 保存时间：24 小时 / 7 天 / 永久
- 忽略应用：可设置不记录某些应用的复制
- 忽略剪贴板管理器：避免冲突
```

## ✂️ 片段替换（Snippets）

### 创建片段

```
Alfred Preferences → Features → Snippets
+ 添加新片段
```

**配置项**：
- **Name** — 片段名称（用于搜索）
- **Keyword** — 触发关键词
- **Snippet** — 替换内容
- **Auto-expand** — 自动展开

### 常用片段示例

| 关键词 | 替换内容 | 用途 |
|--------|----------|------|
| `;email` | `flora@example.com` | 邮箱 |
| `;addr` | `北京市朝阳区...` | 地址 |
| `;phone` | `13800138000` | 电话 |
| `;date` | `2026-06-03` | 当前日期 |
| `;time` | `14:30` | 当前时间 |
| `;sign` | `Best regards, Flora` | 签名 |

### 使用方法

```
F19 → 输入关键词 → Enter
或
在任意输入框中输入关键词 → 自动替换（如果开启 Auto-expand）
```

## 🌐 Web 书签

### 添加 Web 搜索

```
Alfred Preferences → Features → Web Search
+ 添加新搜索
```

**配置示例**：

| 名称 | 关键词 | URL |
|------|--------|-----|
| Google | `g` | `https://google.com/search?q={query}` |
| GitHub | `gh` | `https://github.com/search?q={query}` |
| Stack Overflow | `so` | `https://stackoverflow.com/search?q={query}` |
| Bilibili | `bili` | `https://search.bilibili.com/all?keyword={query}` |
| 百度 | `bd` | `https://www.baidu.com/s?wd={query}` |

### 使用方法

```
F19 → g 搜索内容 → Enter  （Google 搜索）
F19 → gh 项目名 → Enter   （GitHub 搜索）
F19 → so 问题 → Enter     （Stack Overflow 搜索）
```

## 🔢 计算器

### 基础计算

```
F19 → 123 + 456 → Enter
F19 → 100 * 0.8 → Enter
F19 → sqrt(144) → Enter
```

### 单位转换

```
F19 → 100 USD to CNY → Enter
F19 → 100 km to miles → Enter
F19 → 36.5 c to f → Enter
```

### 进制转换

```
F19 → 0xFF → Enter  （十六进制转十进制）
F19 → 0b1010 → Enter（二进制转十进制）
```

## 📇 联系人搜索

### 搜索联系人

```
F19 → 联系人姓名 → Enter
```

**操作选项**：
- 发送邮件
- 打电话
- 发送 iMessage
- 查看联系人卡片

## 🎨 外观自定义

### 主题设置

```
Alfred Preferences → Appearance → Themes
```

**内置主题**：
- Alfred（默认）
- Dark
- Light
- 自定义主题

### 字体和颜色

```
Alfred Preferences → Appearance → Options
- 字体大小
- 窗口透明度
- 高亮颜色
```

## 🔗 相关文档

- [[shortcuts]] — 快捷键大全
- [[advancedFeatures]] — 高级功能
- [[workflows]] — 工作流开发
