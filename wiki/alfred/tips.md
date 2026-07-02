---
type: topic
tags:
  - alfred
  - 技巧
  - 最佳实践
article_id: OBA-ALFRED007
created_at: 2026-06-03
updated_at: 2026-06-03
---

# Alfred 实用技巧

> 本文档整理 Alfred 的实用技巧和最佳实践。

## 🎯 效率技巧

### 1. 快速启动应用

**技巧**：使用拼音首字母快速启动应用

```
F19 → wx → Enter  （打开微信）
F19 → dy → Enter  （打开抖音）
F19 → tb → Enter  （打开淘宝）
```

### 2. 快速计算

**技巧**：直接在 Alfred 中计算

```
F19 → 100 * 0.8 → Enter  （计算 100 的 80%）
F19 → 100 + 20% → Enter  （计算 100 加 20%）
F19 → 100 - 15% → Enter  （计算 100 减 15%）
```

### 3. 单位转换

**技巧**：快速转换单位

```
F19 → 100 USD to CNY → Enter  （美元转人民币）
F19 → 100 km to miles → Enter （公里转英里）
F19 → 36.5 c to f → Enter    （摄氏度转华氏度）
```

### 4. 定义查询

**技巧**：快速查询单词定义

```
F19 → define algorithm → Enter  （查询单词定义）
F19 → define 效率 → Enter       （查询中文定义）
```

## 📋 剪贴板技巧

### 1. 批量复制粘贴

**技巧**：使用剪贴板历史批量处理

```
1. 复制多个内容
2. F19 → ⌘ + ⌥ + C → 打开剪贴板历史
3. 选择需要的内容逐个粘贴
```

### 2. 置顶常用内容

**技巧**：将常用内容置顶

```
1. F19 → ⌘ + ⌥ + C → 打开剪贴板历史
2. 选择内容 → ⌘ + P → 置顶
```

### 3. 搜索历史内容

**技巧**：快速搜索剪贴板历史

```
F19 → ⌘ + ⌥ + C → 输入关键词 → 搜索
```

## ✂️ 片段技巧

### 1. 动态片段

**技巧**：使用动态占位符

```
;date → 2026-06-03  （当前日期）
;time → 14:30       （当前时间）
;year → 2026        （当前年份）
```

### 2. 嵌套片段

**技巧**：片段中引用其他片段

```
;sign → Best regards, Flora
;email-sign → {;sign}\n{;email}
```

### 3. 片段分类

**技巧**：按用途分类片段

```
;e-  → 邮箱相关
;p-  → 电话相关
;a-  → 地址相关
;d-  → 日期相关
```

## 🔍 搜索技巧

### 1. 文件类型过滤

**技巧**：按文件类型搜索

```
F19 → find *.pdf 文件名 → Enter  （搜索 PDF）
F19 → find *.jpg 文件名 → Enter  （搜索图片）
F19 → find *.java 文件名 → Enter （搜索 Java 文件）
```

### 2. 路径搜索

**技巧**：在指定路径搜索

```
F19 → find ~/Documents/ 文件名 → Enter
F19 → find ~/Projects/ 文件名 → Enter
```

### 3. 内容搜索

**技巧**：搜索文件内容

```
F19 → in 关键词 → Enter  （搜索文件内容）
```

## 🎨 界面技巧

### 1. 调整窗口大小

**技巧**：自定义 Alfred 窗口大小

```
Alfred Preferences → Appearance → Options
调整 Width 和 Height
```

### 2. 调整透明度

**技巧**：设置窗口透明度

```
Alfred Preferences → Appearance → Options
调整 Transparency
```

### 3. 自定义主题

**技巧**：创建个性化主题

```
Alfred Preferences → Appearance → Themes
+ 创建新主题
自定义颜色和样式
```

## 🔧 高级技巧

### 1. 使用通配符

**技巧**：使用通配符搜索

```
F19 → find *report* → Enter  （搜索包含 report 的文件）
F19 → find 2026-* → Enter    （搜索 2026 开头的文件）
```

### 2. 使用布尔运算

**技巧**：组合搜索条件

```
F19 → find report AND 2026 → Enter
F19 → find report OR summary → Enter
```

### 3. 使用正则表达式

**技巧**：高级搜索模式

```
F19 → find /pattern/ → Enter  （正则表达式搜索）
```

## 🎯 开发技巧

### 1. 快速打开终端

**技巧**：在当前目录打开终端

```
F19 → find 文件夹 → ⌘ + Enter → 打开终端
```

### 2. 快速打开 IDE

**技巧**：用指定 IDE 打开项目

```
F19 → find 项目文件夹 → ⌘ + \ → 选择 VS Code
```

### 3. Git 快捷操作

**技巧**：创建 Git 快捷工作流

```
F19 → gstat → Enter  （查看 Git 状态）
F19 → glog → Enter   （查看 Git 日志）
F19 → gdiff → Enter  （查看 Git 差异）
```

## 📱 与其他应用配合

### 1. 与 Raycast 配合

**技巧**：互补使用

- Alfred：快速启动、剪贴板、片段
- Raycast：窗口管理、剪贴板增强、AI 功能

### 2. 与终端配合

**技巧**：快速执行终端命令

```
F19 → find 终端应用 → Enter → 执行命令
```

### 3. 与浏览器配合

**技巧**：快速打开网页

```
F19 → g 搜索内容 → Enter  （Google 搜索）
F19 → gh 项目名 → Enter   （GitHub 搜索）
```

## 🚀 性能优化

### 1. 限制搜索范围

**技巧**：优化搜索性能

```
Alfred Preferences → Features → Default Results
限制搜索范围
```

### 2. 清理缓存

**技巧**：定期清理缓存

```
Alfred Preferences → Advanced → Data
清除搜索历史和缓存
```

### 3. 禁用不需要的功能

**技巧**：关闭不使用的功能

```
Alfred Preferences → Features
禁用不需要的功能模块
```

## 🔗 相关文档

- [[basicFeatures]] — 核心功能
- [[advancedFeatures]] — 高级功能
- [[workflows]] — 工作流开发
- [[shortcuts]] — 快捷键大全
