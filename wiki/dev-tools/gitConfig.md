---
type: topic
tags: [dev-tools, git, 版本控制, 别名, 配置]
article_id: OBA-DEVTOOLS003
created_at: 2026/06/02
updated_at: 2026/06/02
---

# Git 配置指南

> 配置文件位置：`~/.gitconfig`
> 备份位置：`~/Documents/SoftwareConfiguration/dotfiles/.gitconfig`

---

## 📋 目录

- [基础配置](#基础配置)
- [别名配置](#别名配置)
- [使用示例](#使用示例)
- [修改理由](#修改理由)
- [常见问题](#常见问题)

---

## 基础配置

### 用户信息

```gitconfig
# ~/.gitconfig 第 4-5 行

[user]
    name = Flora
    email = alexye0602@gmail.com
```

**说明**：设置 Git 提交时的作者信息。

### 推送行为

```gitconfig
# ~/.gitconfig 第 7-8 行

[push]
    default = current      # 推送当前分支到同名远程分支
    autoSetupRemote = true # 首次推送自动设置上游分支
```

**好处**：
- 不需要 `git push origin main`，直接 `git push` 即可
- 新建分支首次推送时自动设置上游分支

### GitHub SSH 配置

```gitconfig
# ~/.gitconfig 第 10-11 行

[url "git@git.com"]
    insteadOf = https://github.com
```

**好处**：自动将 HTTPS 协议转换为 SSH，避免每次输入密码。

### 编辑器

```gitconfig
# ~/.gitconfig 第 14-15 行

[core]
    autocrlf = input    # 统一换行符为 LF
    editor = vim        # 默认编辑器
```

### 合并冲突风格

```gitconfig
# ~/.gitconfig 第 17-18 行

[merge]
    conflictstyle = diff3  # 显示三方合并结果
```

**效果**：显示本地、远程、共同祖先三个版本，更容易理解冲突。

**源码示例**：
```
<<<<<<< HEAD
    // 本地修改
    function hello() {
        return "hello local";
    }
||||||| merged common ancestor
    // 共同祖先
    function hello() {
        return "hello";
    }
=======
    // 远程修改
    function hello() {
        return "hello remote";
    }
>>>>>>> feature-branch
```

### 拉取行为

```gitconfig
# ~/.gitconfig 第 20-21 行

[pull]
    rebase = true  # 拉取时自动 rebase
```

**好处**：`git pull` 时自动 rebase，保持提交历史线性。

**对比**：
```bash
# pull.rebase = false（默认）
git pull
# 结果：创建合并提交
#   * Merge branch 'main' into feature
#   |\ 
#   | * commit B
#   * | commit A
#   |/

# pull.rebase = true
git pull --rebase
# 结果：线性历史
#   * commit A
#   * commit B
```

### 重用记录

```gitconfig
# ~/.gitconfig 第 23-24 行

[rerere]
    enabled = true  # 启用冲突解决方案记录
```

**好处**：Git 自动记住冲突解决方案，下次遇到相同冲突自动应用。

### 默认分支

```gitconfig
# ~/.gitconfig 第 26-27 行

[init]
    defaultBranch = main  # 新仓库默认分支名
```

---

## 别名配置

### 状态和信息

```gitconfig
# ~/.gitconfig 第 33-38 行

[alias]
    # 简洁状态（单行显示）
    st = status -sb
    
    # 完整状态
    s = status
    
    # 查看差异
    df = diff
    dfs = diff --staged
    
    # 图形化日志（所有分支）
    lg = log --oneline --graph --decorate --all
    
    # 详细日志（带作者和文件统计）
    ll = log --pretty=format:"%C(yellow)%h%Cred%d\\ %Creset%s%Cblue\\ [%cn]" --decorate --numstat
    
    # 上次提交详情
    last = log -1 HEAD --stat
```

**使用示例**：
```bash
git st      # 简洁状态
git lg      # 图形化日志
git dfs     # 暂存区差异
```

### 分支操作

```gitconfig
# ~/.gitconfig 第 40-46 行

[alias]
    co = checkout           # 切换分支
    cob = checkout -b       # 创建并切换分支
    br = branch             # 列出分支
    brd = branch -D         # 强制删除分支
    brm = branch -M         # 重命名分支
```

**使用示例**：
```bash
git co main                 # 切换到 main 分支
git cob feature/user-auth   # 创建并切换到新分支
git brd feature/old-branch  # 删除分支
```

### 提交操作

```gitconfig
# ~/.gitconfig 第 48-52 行

[alias]
    cm = commit              # 提交
    cma = commit --amend     # 修改上次提交
    cmn = commit --amend --no-edit  # 追加到上次提交（不改消息）
```

**使用示例**：
```bash
git cm "feat: add new feature"  # 提交
git cma                          # 修改上次提交（打开编辑器）
git cmn                          # 追加文件到上次提交
```

### 暂存操作

```gitconfig
# ~/.gitconfig 第 54-57 行

[alias]
    a = add                  # 添加文件
    aa = add -A              # 添加所有文件
    unstage = reset HEAD --  # 取消暂存
```

**使用示例**：
```bash
git a file.txt    # 添加单个文件
git aa            # 添加所有文件
git unstage file.txt  # 取消暂存
```

### 远程操作

```gitconfig
# ~/.gitconfig 第 59-64 行

[alias]
    f = fetch                # 获取远程更新
    fa = fetch --all         # 获取所有远程更新
    pl = pull                # 拉取并合并
    ps = push                # 推送
    psf = push --force-with-lease  # 安全强制推送
```

**`--force-with-lease` vs `--force`**：
```bash
# --force：无条件强制推送，可能覆盖他人提交
git push --force

# --force-with-lease：检查远程分支是否有新提交，有则拒绝推送
git push --force-with-lease
```

### 暂存（Stash）

```gitconfig
# ~/.gitconfig 第 66-68 行

[alias]
    # 暂存所有（含未跟踪文件）
    stash-all = stash push -u -m
    
    # 美化的暂存列表
    stash-list = stash list --format='%C(yellow)%h%C(reset) %gs (%C(blue)%cr%C(reset))'
```

### 清理

```gitconfig
# ~/.gitconfig 第 70-72 行

[alias]
    clean-all = clean -fd         # 清理未跟踪的文件和目录
    prune = remote prune origin   # 清理已删除的远程分支引用
```

### 标签

```gitconfig
# ~/.gitconfig 第 74-75 行

[alias]
    tag-list = tag -l --sort=-v:refname  # 按版本排序的标签列表
```

---

## 颜色配置

### 分支颜色

```gitconfig
# ~/.gitconfig 第 85-88 行

[color "branch"]
    current = yellow bold  # 当前分支
    local = green          # 本地分支
    remote = cyan          # 远程分支
```

### 差异颜色

```gitconfig
# ~/.gitconfig 第 90-95 行

[color "diff"]
    meta = yellow bold     # 元信息
    frag = magenta bold    # 差异位置
    old = red bold         # 删除的行
    new = green bold       # 新增的行
```

### 状态颜色

```gitconfig
# ~/.gitconfig 第 97-100 行

[color "status"]
    added = green          # 新增文件
    changed = yellow       # 修改文件
    untracked = red        # 未跟踪文件
```

---

## 使用示例

### 日常开发流程

```bash
# 1. 查看状态
git st

# 2. 创建功能分支
git cob feature/user-auth

# 3. 开发完成后，添加并提交
git aa
git cm "feat: add user authentication"

# 4. 推送到远程
git ps

# 5. 切换回主分支
git co main

# 6. 拉取最新代码
git pl

# 7. 合并功能分支
git merge feature/user-auth

# 8. 删除功能分支
git brd feature/user-auth
```

### 修改上次提交

```bash
# 发现提交消息写错了
git cma
# 会打开编辑器，修改提交消息

# 发现漏了文件
git add forgotten-file.txt
git cmn
# 追加到上次提交，不修改提交消息
```

### 安全强制推送

```bash
# rebase 后需要强制推送
git rebase main
git psf  # 使用 --force-with-lease，更安全
```

### 查看分支历史

```bash
# 图形化查看所有分支
git lg

# 查看某个分支的提交
git lg feature/user-auth

# 查看最近 5 次提交
git lg -5
```

### 暂存工作

```bash
# 临时切换分支，暂存当前工作
git stash-all "WIP: user auth"

# 查看暂存列表
git stash-list

# 恢复暂存
git stash pop
```

---

## 修改理由

### 1. 添加常用别名

**问题**：
- Git 命令较长，输入效率低
- 一些命令参数难以记忆

**解决方案**：
- 添加 20+ 常用别名
- 按功能分组，便于记忆
- 使用简短的缩写

### 2. 设置 autoSetupRemote

**问题**：
- 新建分支首次推送需要指定上游分支
- 容易忘记 `git push -u origin branch-name`

**解决方案**：
```gitconfig
[push]
    autoSetupRemote = true
```

### 3. 启用 pull.rebase

**问题**：
- `git pull` 默认创建合并提交
- 提交历史变得混乱

**解决方案**：
```gitconfig
[pull]
    rebase = true
```

### 4. 使用 --force-with-lease

**问题**：
- `git push --force` 可能覆盖他人提交
- 不安全

**解决方案**：
```gitconfig
[alias]
    psf = push --force-with-lease
```

### 5. 启用 rerere

**问题**：
- 重复遇到相同冲突需要重新解决
- 浪费时间

**解决方案**：
```gitconfig
[rerere]
    enabled = true
```

### 6. 设置 conflictstyle = diff3

**问题**：
- 默认冲突显示不够清晰
- 难以理解冲突原因

**解决方案**：
```gitconfig
[merge]
    conflictstyle = diff3
```

---

## 常见问题

### Q: 如何查看所有别名？

```bash
git config --get-regexp alias
```

### Q: 如何临时使用原始命令？

```bash
git commit          # 使用别名
command git commit  # 使用原始命令
```

### Q: 如何删除别名？

```bash
git config --global --unset alias.st
```

### Q: --force-with-lease 和 --force 有什么区别？

- `--force`：无条件强制推送，可能覆盖他人提交
- `--force-with-lease`：检查远程分支是否有新提交，有则拒绝推送

### Q: 如何配置 Delta（更好的 diff 显示）？

```bash
# 安装 delta
brew install git-delta

# 编辑 ~/.gitconfig，添加：
[core]
    pager = delta

[delta]
    navigate = true
    side-by-side = true
    line-numbers = true
```

---

## 相关页面

- [[Git]] — Git 基础命令
- [[gitignore]] — Gitignore 配置指南
- [[zshAndShell]] — Zsh 配置指南

## 外部资源

- [Git 官方文档](https://git-scm.com/doc)
- [Git 别名配置](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git-%E5%88%AB%E5%90%8D)
- [Delta 官方文档](https://github.com/dandavison/delta)
