title: 你所不知道的git技巧 
description: 你所不知道的git使用技巧
date: 2015-12-17 22:52:13
categories:
- other

tags: [Git]
---
Git是我们在项目开发中所使用的版本管理工具，一些常见的基本命令也许我们很熟悉了，下面介绍一些你所不常见但很有用的git使用技巧。<!-- more -->

#### 在log中显示branch和tag
```
git log --oneline --decorate
```

![](/images/git_log_branch_tag.png)

#### 推送分支并关联
```
git push -u origin master
```

推送本地分支到远端并自动建立关联，如果是用`git pull`远端的分支，在做了修改后可以直接使用`git push`命令推送，而不需要加其他参数。然而，`git push` 会把所有的分支都推送到远端，并且赋以相同的分支名称。为了只推送当前的分支，可以如下配置：
```
git config --global push.default tracking
```

#### track远程分支
如果你想在远端某个现有分支上进行修改并提交，可以如下操作：
```
git checkout -t origin/feature
```
这会在你的本地创建一个关联的feature分支，完成操作后可以直接`git push`到远端。

#### 找出修改属于哪个分支
```
git branch --contains 50f3754
```
这会帮你找出所有包含这个commit的分支，如果加上`-a`标识，则还会在远端分支中查找。

#### 找出满足正则匹配的最后一条commit
```
git show :/fix
git show :/^Merge
```

#### stash 未提交的内容
如果我们正在开发一个新的feature，现在需要我们修改一个紧急的bug，我们不想提交未完成的新feature，也不想丢失我们已经完成的那部分工作，此时我们可以用`stash`命令来临时保存我们的修改。
```
git stash
```
此时会隐藏我们所做的修改，并且保持一个干净的工作环境，现在我们可以基于这个分支创建一个新的分支来修改bug，当我们完成bug修改后，我们可以在切换到之前的干净分支，并且：
```
git stash pop
```
可以把你之前的部分工作还原回来。如果你想把之前stash的内容删除掉，可以:
```
git stash drop
```
此外，查看所有stash列表和清除所有stash如下：
```
git stash list
git stash clear
```
#### 查看不同版本间的文件差异
```
git diff $start_commit..$end_commit -- path/to/file
```
或者直接比较两次提交纪录之间的所有修改：
```
git diff $start_commit..$end_commit
```

#### 撤销修改
如果你需要撤销未提交的修改内容，可以：
```
git reset --hard HEAD
```
或者撤销某个文件：
```
git checkout HEAD -- path/to/file
```
如果已经提交了文件修改，仍然想revert的话，可以：
```
git reset --soft HEAD~1
```
#### 推送远端前清理提交纪录
在推送远端前，本地的分支中可能有许多冗余的提交纪录，此时可以合并或删除部分提交纪录信息，让整个commit更干净。
如：
```
git log:
Commit A
Commit B
Commit C
Commit D
Commit 0
```
比如要合并A，B，C，D为一个commit
```
git rebase -i Commit 0
```
在B C D前面设s，退出rebase，编辑commit，这时就成了：
```
Commit X
Commit 0
```

#### 解决冲突
当我们使用git pull/push 或git merge时，有时会产生一些冲突，可能有些部分文件需要我们手动来解决，还有些冲突文件我们可以确定要么是保留我们local版本的修改，要么是使用remote/other分支的修改。
如果我们对所有的冲突文件都保留local或remote分支的修改，那么我们可以：`cd project-folder`，然后搜索所有的冲突文件：`grep -lr '<<<<<<<' ./`。执行：
```
git checkout --ours PATH/FILE
```
或
```
git checkout --theirs PATH/FILE
```
也可以用如下的流处理：
```
grep -lr '<<<<<<<' . | xargs git checkout --ours
```
或
```
grep -lr '<<<<<<<' . | xargs git checkout --theirs
```

#### 利用git hooks
在.git/hooks中有许多hook文件，修改这些hook可以帮助我们进行一些检查，如对js和css进行格式化检查，提交信息是否满足项目的要求。
