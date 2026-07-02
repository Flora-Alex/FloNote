---
type: topic
tags: [dev-tools, git, gitignore, 忽略规则, 配置]
article_id: OBA-DEVTOOLS004
created_at: 2026/06/02
updated_at: 2026/06/02
---

# Gitignore 配置指南

> 配置文件位置：`~/.gitignore_global`
> 备份位置：`~/Documents/SoftwareConfiguration/dotfiles/.gitignore_global`

---

## 📋 目录

- [概述](#概述)
- [macOS 系统文件](#macos-系统文件)
- [IDE 和编辑器](#ide-和编辑器)
- [Java 项目](#java-项目)
- [Node.js 项目](#nodejs-项目)
- [Python 项目](#python-项目)
- [安全相关](#安全相关)
- [修改理由](#修改理由)
- [常见问题](#常见问题)

---

## 概述

全局 `.gitignore` 文件用于指定所有 [[Git]] 仓库都应该忽略的文件模式。

### 配置方式

```gitconfig
# ~/.gitconfig 第 103-104 行

[exclude]
    file = ~/.gitignore_global
```

### 优先级

1. `.git/info/exclude` — 仓库特定
2. `.gitignore` — 仓库特定
3. `~/.gitignore_global` — 全局（本文件）

---

## macOS 系统文件

```gitignore
# ~/.gitignore_global 第 5-16 行

# macOS 系统文件
*~
.DS_Store
.AppleDouble
.LSOverride
Icon
._*
.Spotlight-V100
.Trashes
.fseventsd
.TemporaryItems
.VolumeIcon.icns
.com.apple.timemachine.donotpresent
```

### 文件说明

| 文件 | 说明 |
|------|------|
| `.DS_Store` | 目录元数据（图标位置等） |
| `._*` | 资源分支文件 |
| `.Spotlight-V100` | Spotlight 索引 |
| `.Trashes` | 废纸篓 |
| `.fseventsd` | 文件系统事件日志 |

---

## IDE 和编辑器

### JetBrains（IntelliJ IDEA、WebStorm 等）

```gitignore
# ~/.gitignore_global 第 19-24 行

.idea/
*.iml
*.iws
*.ipr
out/
```

### Visual Studio Code

```gitignore
# ~/.gitignore_global 第 26-27 行

.vscode/
*.code-workspace
```

### Vim

```gitignore
# ~/.gitignore_global 第 29-34 行

*.swp
*.swo
*~
Session.vim
.netrwhist
tags
```

---

## Java 项目

### 编译产物

```gitignore
# ~/.gitignore_global 第 43-48 行

*.class
*.jar
*.war
*.ear
*.nar
target/
```

### Maven

```gitignore
# ~/.gitignore_global 第 50-52 行

.mvn/timing.properties
.mvn/wrapper/maven-wrapper.jar
```

### Gradle

```gitignore
# ~/.gitignore_global 第 54-57 行

.gradle/
build/
!gradle/wrapper/gradle-wrapper.jar
```

### Spring Boot

```gitignore
# ~/.gitignore_global 第 59 行

*.pid
```

### 排除规则

```gitignore
# ~/.gitignore_global 第 61-64 行

# 保留特定文件
!.mvn/wrapper/maven-wrapper.jar
!**/src/main/**/target/
!**/src/test/**/target/
```

---

## Node.js 项目

### 依赖和缓存

```gitignore
# ~/.gitignore_global 第 68-77 行

node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.pnp
.pnp.js
.npm
.eslintcache
.stylelintcache
```

### 构建产物

```gitignore
# ~/.gitignore_global 第 79-83 行

dist/
build/
out/
output/
*.tgz
```

---

## Python 项目

### 编译文件

```gitignore
# ~/.gitignore_global 第 97-101 行

__pycache__/
*.py[cod]
*$py.class
*.so
```

### 虚拟环境

```gitignore
# ~/.gitignore_global 第 103-106 行

.venv/
venv/
ENV/
env/
```

### 分发文件

```gitignore
# ~/.gitignore_global 第 108-117 行

develop-eggs/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
*.egg-info/
.installed.cfg
*.egg
```

---

## 安全相关

### 环境变量

```gitignore
# ~/.gitignore_global 第 86-90 行

.env
.env.local
.env.development.local
.env.test.local
.env.production.local
```

### 密钥文件

```gitignore
# ~/.gitignore_global 第 92-95 行

*.pem
*.key
*.crt
*.p12
```

**重要**：永远不要将密钥文件提交到 [[Git]] 仓库！

---

## 日志文件

```gitignore
# ~/.gitignore_global 第 120-124 行

*.log
logs/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
```

## 测试覆盖率

```gitignore
# ~/.gitignore_global 第 126-128 行

coverage/
.nyc_output/
*.lcov
```

## 数据库

```gitignore
# ~/.gitignore_global 第 130-132 行

*.sqlite
*.sqlite3
*.db
```

## 临时文件

```gitignore
# ~/.gitignore_global 第 134-137 行

*.tmp
*.temp
*.bak
*.backup
*.orig
```

---

## 修改理由

### 1. 忽略 .DS_Store

**问题**：
- macOS 在每个目录创建 `.DS_Store`
- 包含图标位置等元数据
- 对项目无用，造成提交混乱

**解决方案**：
```gitignore
.DS_Store
```

**从仓库中移除已有的 .DS_Store**：
```bash
find . -name ".DS_Store" -delete
git rm -r --cached .
git add .
git commit -m "Remove .DS_Store files"
```

### 2. 忽略 IDE 配置

**问题**：
- 不同开发者使用不同 IDE
- IDE 配置文件对项目无用
- 造成不必要的冲突

**解决方案**：
```gitignore
.idea/
.vscode/
*.iml
```

### 3. 忽略 node_modules

**问题**：
- `node_modules` 可能包含数千个文件
- 体积庞大（几百 MB）
- 可以通过 `package.json` 重建

**解决方案**：
```gitignore
node_modules/
```

**重建依赖**：
```bash
npm install
```

### 4. 忽略编译产物

**问题**：
- 编译产物（target、dist、build）是生成的
- 不应该纳入版本控制
- 不同环境可能产生不同结果

**解决方案**：
```gitignore
target/
dist/
build/
```

### 5. 忽略密钥文件

**问题**：
- 密钥文件（.env、.pem、.key）包含敏感信息
- 提交到仓库会造成安全风险
- 一旦泄露，需要立即轮换密钥

**解决方案**：
```gitignore
.env
*.pem
*.key
```

**替代方案**：
- 使用环境变量
- 使用密钥管理服务（如 AWS Secrets Manager、HashiCorp Vault）
- 使用 `.env.example` 文件作为模板

---

## 忽略规则语法

```gitignore
# 注释
*           # 匹配任意字符
**          # 匹配任意目录
!           # 取反（不忽略）
/           # 目录
```

### 示例

```gitignore
*.log               # 忽略所有 .log 文件
!important.log      # 不忽略 important.log
/build              # 只忽略根目录的 build
doc/**              # 忽略 doc 目录下所有文件
**/foo              # 忽略任意目录下的 foo
```

---

## 常见问题

### Q: 如何添加项目特定的忽略规则？

在项目根目录创建 `.gitignore` 文件：

```bash
# 项目特定的忽略规则
*.local
config/local.yml
```

### Q: 如何忽略已跟踪的文件？

```bash
# 从跟踪中移除（不删除文件）
git rm --cached filename

# 从跟踪中移除目录
git rm -r --cached directory/
```

### Q: 如何查看哪些文件被忽略？

```bash
# 查看被忽略的文件
git status --ignored

# 检查特定文件是否被忽略
git check-ignore -v filename
```

### Q: 如何强制添加被忽略的文件？

```bash
git add -f filename
```

### Q: 如何清理仓库中已有的忽略文件？

```bash
# 删除所有 .DS_Store
find . -name ".DS_Store" -delete

# 从 Git 中移除
git rm -r --cached .
git add .
git commit -m "Remove ignored files"
```

---

## 最佳实践

1. **项目特定规则**放在项目的 `.gitignore`
2. **通用规则**放在全局 `~/.gitignore_global`
3. **定期检查**并更新忽略规则
4. **不要忽略**项目必需的配置文件
5. **密钥文件**永远不要提交

---

## 相关页面

- [[Git]] — Git 基础命令
- [[gitConfig]] — Git 配置指南

## 外部资源

- [Gitignore 官方模板](https://github.com/github/gitignore)
- [Gitignore 文档](https://git-scm.com/docs/gitignore)
