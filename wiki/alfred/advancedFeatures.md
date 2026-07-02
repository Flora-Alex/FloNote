---
type: topic
tags:
  - alfred
  - 进阶
  - 高级功能
article_id: OBA-ALFRED005
created_at: 2026-06-03
updated_at: 2026-06-03
---

# Alfred 进阶功能

> 本文档介绍 Alfred 的高级功能，包括 Universal Actions、Fallback Searches 等。

## 🎬 Universal Actions

Universal Actions 是 Alfred 的强大功能，可以对选中的内容执行多种操作。

### 使用方法

1. 在 Alfred 中选中一个结果
2. 按 `⌘ + \` 或 `→` 打开 Universal Actions
3. 选择要执行的操作

### 可用操作

#### 文件操作

| 操作 | 说明 |
|------|------|
| Open | 打开文件 |
| Open with... | 用指定应用打开 |
| Reveal in Finder | 在 Finder 中显示 |
| Copy to... | 复制到指定位置 |
| Move to... | 移动到指定位置 |
| Delete | 删除文件 |
| Rename | 重命名文件 |
| Get Info | 获取文件信息 |

#### 文本操作

| 操作 | 说明 |
|------|------|
| Copy to Clipboard | 复制到剪贴板 |
| Paste to Frontmost App | 粘贴到当前应用 |
| Save to File | 保存到文件 |
| Email | 发送邮件 |
| Tweet | 发推 |
| Translate | 翻译 |
| Search | 搜索 |

#### URL 操作

| 操作 | 说明 |
|------|------|
| Open URL | 打开链接 |
| Copy URL | 复制链接 |
| Bookmark | 添加书签 |
| Download | 下载文件 |

### 自定义 Universal Actions

```
Alfred Preferences → Features → Universal Actions
+ 添加自定义操作
```

## 🔍 Fallback Searches

Fallback Searches 是当 Alfred 找不到结果时提供的备选搜索。

### 配置 Fallback Searches

```
Alfred Preferences → Features → Default Results
Fallback Searches
```

**推荐配置**：
- Google 搜索
- Wikipedia
- Amazon
- YouTube
- 自定义 Web 搜索

### 使用方法

```
F19 → 输入内容 → 没有匹配结果 → 显示 Fallback Searches
```

## 📂 文件过滤器

### 创建文件过滤器

```
Alfred Preferences → Features → File Search
+ 添加文件过滤器
```

**配置示例**：

| 名称 | 关键词 | 过滤器 |
|------|--------|--------|
| 图片 | `img` | `kMDItemContentType = 'public.image'` |
| 文档 | `doc` | `kMDItemContentType = 'com.adobe.pdf'` |
| 代码 | `code` | `kMDItemFSName = '*.java' OR kMDItemFSName = '*.py'` |
| 视频 | `vid` | `kMDItemContentType = 'public.movie'` |

### 使用方法

```
F19 → img 文件名 → Enter  （搜索图片）
F19 → doc 文件名 → Enter  （搜索文档）
F19 → code 文件名 → Enter （搜索代码文件）
```

## 🎨 主题自定义

### 创建自定义主题

```
Alfred Preferences → Appearance → Themes
+ 创建新主题
```

### 主题配置

| 配置项 | 说明 |
|--------|------|
| Background | 背景颜色/图片 |
| Window | 窗口样式 |
| Result | 结果样式 |
| Highlight | 高亮颜色 |
| Text | 字体和颜色 |
| Size | 窗口大小 |

### 导入主题

```
Alfred Preferences → Appearance → Themes
Import → 选择 .alfredappearance 文件
```

## 🔗 应用扩展

### 安装应用扩展

许多 macOS 应用提供了 Alfred 扩展：

- **1Password** — 密码管理
- **Bear** — 笔记应用
- **Things** — 任务管理
- **Spotify** — 音乐播放

### 配置应用扩展

```
Alfred Preferences → Features → 选择应用
配置扩展选项
```

## 📊 使用统计

### 查看使用统计

```
Alfred Preferences → Advanced → Stats
```

**统计数据**：
- 启动次数
- 最常用应用
- 搜索次数
- 剪贴板使用

## 🔧 高级设置

### 权限管理

```
Alfred Preferences → Advanced → Permissions
```

**权限选项**：
- 辅助功能权限
- 磁盘访问权限
- 完全磁盘访问

### 性能优化

```
Alfred Preferences → Advanced → Performance
```

**优化选项**：
- 搜索范围限制
- 结果数量限制
- 缓存设置

### 数据管理

```
Alfred Preferences → Advanced → Data
```

**管理选项**：
- 清除搜索历史
- 清除剪贴板历史
- 重置学习数据

## 🎯 实用技巧

### 1. 快速计算

```
F19 → 100 * 0.8 → Enter  （计算 100 的 80%）
F19 → 100 + 20% → Enter  （计算 100 加 20%）
F19 → 100 - 15% → Enter  （计算 100 减 15%）
```

### 2. 单位转换

```
F19 → 100 USD to CNY → Enter  （美元转人民币）
F19 → 100 km to miles → Enter （公里转英里）
F19 → 36.5 c to f → Enter    （摄氏度转华氏度）
```

### 3. 定义查询

```
F19 → define algorithm → Enter  （查询单词定义）
F19 → define 效率 → Enter       （查询中文定义）
```

### 4. 联系人快速操作

```
F19 → 联系人姓名 → Enter → 选择操作
- 发送邮件
- 打电话
- 发送 iMessage
- 复制联系方式
```

## 🔗 相关文档

- [[basicFeatures]] — 核心功能
- [[workflows]] — 工作流开发
- [[tips]] — 实用技巧
