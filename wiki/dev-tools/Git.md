---
type: summary | topic | cheatsheet | knowledge | (AI可根据具体情况设定)
tags:
  - 知识标签
article_id: OBA-xxxxxxxx
created_at: 2026-05-07T17:59:00
last_updated: 2026-05-07T17:59:00
---
- 如何提交多次 commit 信息?
    
    - 方法一：使用 `git reset --soft #commit id` 回退后，再重新提交。
    
    - 方法二：使用 `git commit --amend -m "commit信息"`
- 如何添加 git 账号
    
    - `git config --global user.name "xxxxx"`
    
    - `git config --global user.email "xxx@qq.com"`
    
    - 查询：`git config --list`
- 忘记将文件添加到 `.gitignore` 但又不想从本地环境中删除。
    
    - 方法一：将文件移出暂存区，即使文件不再被tracked  
        注：此操作会产生一次删除文件的记录。
        
        - 1. `git rm --cached filename.txt`
        
        - 2. `git rm -r --cached folder`
    
    - 方法二：直接删除远程库（一般情况没有权限）  
        注：[https://www.cnblogs.com/Edmondhui/p/15898692.html](https://www.cnblogs.com/Edmondhui/p/15898692.html)
    
    - 方法三：使用 cherrypick 功能
- 如何修改历史的 author 或重新填写 commit 信息？
    
    - 参考资料：[stackoverflow](https://stackoverflow.com/questions/3042437/how-to-change-the-commit-author-for-a-single-commit)
    
    - 举例：如果 commit 历史是 A-B-C-D-E-F ，其中 F 是 HEAD，假设希望修改的是 C 和 D 的 author 信息或 date 信息，可以按照以下步骤操作：
        
        - 1. 使用 `git rebase -i B`变基到需要修改的前一个 commit id ，即 B 。
            - 如果需要从头修改，可以使用 `git reabase -i --root` 。
        
        - 2. 此时会进入 vim 编辑器模式，按下 i 可进入修改模式，对需要修改的 commit id 将 pick修改为edit 。
        
        - 3. 按下 ESC 模式推出修改模式，再按下 :wq 保存退出。
        
        - 4. 一旦 rebase 开始，就会立马停在 C 次提交。
        
        - 5. 开始执行修改操作
            
            - 修改作者信息：`git commit --amend --author="Author Name <email@address.com>"`
            
            - 修改修改时间：`git commit --amend --date="2023-06-16 10:30:00"`![](https://api2.mubu.com/v3/document_image/7936039c-3fdd-4f15-93e7-79a4198c0d09-350644.jpg?)
        
        - 6. 如何还有分支需要修改，继续：`git rebase --continue`，此时会停在 D (在第2步中被修改为 edit 的分支)。
        
        - 7. 修改 D 分支内容后，再执行 `git rebase --continue`，直至所有 rebase 完成。
        
        - 8. 如果分支已提交，则执行 `git push -f `更新提交历史。
- 如何删除分支：
    
    - 本地分支删除：`git branch -d <branchname>`
    
    - 远程分支删除：`git push origin --delete dev[待删除分支]`
- 如何修改本地分支名：
    - `git branch -m oldName newName`
- 如何打tag?
    
    - 1.直接在分支上打 tag
        - `git tag v1.0.0`
    
    - 2. 为历史版本打 tag
        - git tag v1.0.0 #commitId
    
    - 3. 创建带有说明的标签
        - `git tag -a v1.0.0 -m "本次tag更新具有说明" commitId`
    
    - 4. 展示 tag
        - `git show <tagname>`
- 如何配置 SSH ？
    
    - 终端：`ssh-keygen -t rsa -C 891712@cibdev.com`
    
    - 会生成` .ssh/id_rsa.pub` （公钥）
    
    - 将公钥配置到 GitLab 中 SSH Keys 中。
- 如何配置多个SSH ？
    
    - 公司秘钥 ssh key（默认 id_rsa.pub）
        - `ssh-keygen -t rsa -C '邮箱' -f ~/.ssh/gitlab_rsa`
    
    - github ssh key（默认 id_rsa.pub）
        - `ssh-keygen -t rsa -C '邮箱' -f ~/.ssh/github_rsa`
    
    - `~/.ssh/config` 文件![](https://api2.mubu.com/v3/document_image/350644_a63b9a91-72ff-49f4-f60b-8121d06a6c00.png?)  
        可以为各个终端配置对应配置，公司的 gitlab 为默认ssh，即读取的是 `id_rsa` ，否则可以配置路径。
    
    - 将公钥配置到 GitLab 中 SSH Keys 中
    
    - 测试 SSH 连接
        
        - 访问 github：`ssh -T git@github.com`
            
            - 支持指定秘钥文件测试：`ssh -T -i ~/.ssh/github_rsa git@github.com`
            
            - 支持指定端口号测试：`ssh -T -p 443 git@ssh.github.com`
        
        - 访问公司：`ssh -T git@git.dev.sh.ctripcorp.com`
    
    - 根据 ssh 日志排查报错
        - `ssh -vvv git@git.dev.sh.ctripcorp.com`
- git revert 和 git reset 的区别？
    - git revert 如何后续提交代码？
        - git reset 到分支后，使用 git stash 和 git stash pop 弹出的方式，新增代码
- 如何只 clone 单次提交记录？
    - `git clone xxx.git -b <分支名> 
        相当于 git clone 后，再执行 `git checkout <分支>`
    - `git clone xxx.git -b <分支名> --single-branch` ：只 clone 单次提交的代码
    - `git clone -b 17.0.2 -- xxxx.git`：只 clone 特定的一个分支。
- 如何解决`remote: You are not allowed to upload code.`
- 致命错误：无法访问 'http://git.dev.sh.ctripcorp.com/ottd/xtaro-tnt-trip.git/'：The requested URL returned error: 403
    - 找到钥匙串，删除其中的 git 相关的秘钥。
    - 登录后，输入账号和密码