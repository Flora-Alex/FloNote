---
type: topic
tags:
  - alfred
  - 工作流
  - 自动化
  - workflow
article_id: OBA-ALFRED006
created_at: 2026-06-03
updated_at: 2026-06-03
---

# Alfred 工作流开发指南

> 本文档介绍 Alfred 工作流（Workflow）的开发和使用方法。

## 📋 什么是工作流

工作流是 Alfred 最强大的功能，可以将多个操作串联起来，实现自动化任务。

**工作流的特点**：
- 可以串联多个操作
- 支持条件判断
- 可以调用外部脚本
- 支持自定义界面

## 🎯 工作流类型

### 1. 输入触发器（Triggers）

| 触发器 | 说明 | 用途 |
|--------|------|------|
| Keyword | 关键词触发 | `F19 → 关键词 → Enter` |
| Hotkey | 快捷键触发 | 全局快捷键 |
| File Action | 文件操作触发 | 对文件执行操作 |
| External Trigger | 外部触发 | 由其他应用触发 |

### 2. 动作（Actions）

| 动作 | 说明 | 用途 |
|------|------|------|
| Open File | 打开文件 | 打开应用程序或文件 |
| Run Script | 运行脚本 | 执行 Shell/Python/AppleScript |
| Open URL | 打开链接 | 打开网页 |
| Copy to Clipboard | 复制到剪贴板 | 复制内容 |
| Notification | 显示通知 | 显示系统通知 |

### 3. 工具（Utilities）

| 工具 | 说明 | 用途 |
|------|------|------|
| Conditional | 条件判断 | 根据条件执行不同操作 |
| Transform | 数据转换 | 转换数据格式 |
| Filter | 过滤 | 过滤数据 |
| Delay | 延迟 | 延迟执行 |
| Loop | 循环 | 循环执行 |

## 🔧 工作流开发

### 创建工作流

```
Alfred Preferences → Workflows
+ → Blank Workflow
```

**配置项**：
- **Name** — 工作流名称
- **Description** — 描述
- **Category** — 分类
- **Bundle Id** — 唯一标识

### 添加触发器

1. 从左侧面板拖拽触发器到画布
2. 配置触发器参数
3. 连接到动作

### 添加动作

1. 从左侧面板拖拽动作到画布
2. 配置动作参数
3. 连接到其他动作或输出

### 连接节点

- 从一个节点的输出端口拖拽到另一个节点的输入端口
- 可以创建分支和循环

## 📝 工作流示例

### 示例 1：快速打开项目

**功能**：快速打开 ~/Projects 下的项目

**配置**：
1. 添加 Keyword 触发器：`proj`
2. 添加 Run Script 动作：
```bash
# 列出项目目录
ls -d ~/Projects/*/
```
3. 添加 List Filter 动作：显示项目列表
4. 添加 Open File 动作：打开选中的项目

### 示例 2：Git 快捷操作

**功能**：快速执行 Git 命令

**配置**：
1. 添加 Keyword 触发器：`gstat`
2. 添加 Run Script 动作：
```bash
# 执行 git status
cd ~/Projects/current-project
git status
```
3. 添加 Copy to Clipboard 动作：复制结果

### 示例 3：Maven 构建

**功能**：快速执行 Maven 构建

**配置**：
1. 添加 Keyword 触发器：`mbuild`
2. 添加 Run Script 动作：
```bash
# 执行 Maven 构建
cd ~/Projects/current-project
mvn clean install
```
3. 添加 Notification 动作：显示构建结果

## 🐚 脚本语言支持

### Shell 脚本

```bash
#!/bin/bash
# 输入参数
query="$1"

# 执行命令
echo "执行命令: $query"
```

### Python 脚本

```python
#!/usr/bin/python3
import sys

# 输入参数
query = sys.argv[1]

# 执行逻辑
print(f"执行命令: {query}")
```

### AppleScript

```applescript
-- 执行 AppleScript
tell application "Finder"
    activate
end tell
```

### Ruby 脚本

```ruby
#!/usr/bin/ruby
# 输入参数
query = ARGV[0]

# 执行逻辑
puts "执行命令: #{query}"
```

## 🎨 工作流界面

### 创建列表界面

```
添加 List Filter 动作
配置列表项：
- Title — 标题
- Subtitle — 副标题
- Icon — 图标
- UID — 唯一标识
- Arg — 参数
```

### 创建文件选择界面

```
添加 File Action 触发器
配置文件类型过滤器
```

## 📦 工作流管理

### 导出工作流

```
Alfred Preferences → Workflows → 选择工作流
右键 → Export...
```

### 导入工作流

```
Alfred Preferences → Workflows
+ → Import → 选择 .alfredworkflow 文件
```

### 分享工作流

- 导出为 `.alfredworkflow` 文件
- 上传到 [Alfred Gallery](https://alfred.app/workflows/)
- 分享给其他用户

## 🌟 推荐工作流

### 开发相关

| 工作流 | 说明 | 下载 |
|--------|------|------|
| Gitmoji | Git 提交表情 | [下载](https://alfred.app/workflows/alfredapp/gitmoji) |
| Color Converter | 颜色转换 | [下载](https://alfred.app/workflows/alfredapp/color-converter) |
| JSON Parser | JSON 解析 | [下载](https://alfred.app/workflows/alfredapp/json-parser) |

### 效率工具

| 工作流 | 说明 | 下载 |
|--------|------|------|
| Kill Process | 杀死进程 | [下载](https://alfred.app/workflows/alfredapp/kill-process) |
| Empty Trash | 清空废纸篓 | [下载](https://alfred.app/workflows/alfredapp/empty-trash) |
| Toggle Dark Mode | 切换深色模式 | [下载](https://alfred.app/workflows/alfredapp/toggle-dark-mode) |

### 搜索相关

| 工作流 | 说明 | 下载 |
|--------|------|------|
| Google Search | Google 搜索 | [下载](https://alfred.app/workflows/alfredapp/google-search) |
| GitHub Search | GitHub 搜索 | [下载](https://alfred.app/workflows/alfredapp/github-search) |
| Stack Overflow | Stack Overflow 搜索 | [下载](https://alfred.app/workflows/alfredapp/stack-overflow) |

## 🔗 相关文档

- [[basicFeatures]] — 核心功能
- [[advancedFeatures]] — 高级功能
- [[tips]] — 实用技巧
