# Git

## Git简单工作流

1. 从 master checkout 一个新的 feature 分支出来进行开发
2. 开发完，执行 `git add` 将代码提交到暂存区
3. `git commit` 提交到本地仓库
4. `git push` 提交到远端分支
5. 开发完所有需求之后，设立一个专门的测试分支比如名叫 dev 的分支，把代码合并到这个测试分支上，发布测试环境进行测试。
6. 测试完毕，执行 `merge request` ，代码走 CR 流程合并到 master 分支。
7. 在提交 MR 的过程中，将 master 分支的代码合并到当前需要被合并的分支，提交并解决冲突。

## 命令

### git stash

- `git stash save 'xxx'` : 储存变更
- `git stash list` : 查看储存区所有提交列表
- `git stash pop` : 弹出并应用最近的一次储存区的代码提交
- `git stash drop stash@{n}` : 删除某次储存记录
- `git stash clear` : 清除所有 stash 信息

### git clone

克隆代码到本地指定分支：`git clone xxx.git -b branch_name`

### git init

初始化本地仓库

### git remote

绑定远程仓库

- `git remote add` : 添加一个远程版本库关联
- `git remote rm` : 删除某个远程版本库关联

### git branch

查看分支

- `git branch` ：查看本地所有分支信息
- `git branch -r` ：查看远程仓库所有分支
- `git branch -a` ：查看本地和远程仓库所有分支

### git checkout

创建并切换到指定新分支：`git checkout -b branch_name`

### git add

- `git add [file1] [file2]` : 添加一个或多个文件到暂存区
- `git add .` ：把当前目录下得所有文件改动都添加到暂存区
- `git add -A` ：把当前仓库内所有文件改动都添加到暂存区

### git commit

`git commit -am` ：暂存、提交一条龙

`git commit [file1] ... -m [message]`：将暂存区的内容提交到本地 git 版本仓库中

- `m` 表示的是当前提交的信息
- `a` 对于已经被纳入 git 管理的文件（该文件你之前提交过 commit），相当于执行了 `git add -A`

### git rm

- `git rm .env`：执行完这个命令就表示 .env 文件从 git 仓库中删除了，配合 .gitignore 就能保证以后所有的 .env 文件变更都不用担心被提交到远程仓库。
- `git rm -r dist`：如果我们要删除的是一个目录，加上 `r` 。

### git push

推送分支并建立关联关系：`git push --set-upstream origin branch_name`

### git pull

还没和远端建立联系并产生冲突时，指定远端分支合并到本地当前分支：`git pull origin branch_name` ，解决冲突后，再执行 `git push --set-upstream origin branch_name` 命令

### git fetch

先拉远端分支更新 `git fetch` ，后提交本地代码，然后合并远端分支 `git merge origin/branch_name`

### git merge

远端分支更新，同时本地要提交代码并合并到远端分支：

1. 切到 master 分支，`git pull` 拉一下最新代码
2. 切回开发分支，执行 `git merge master` 合并一下 master 代码

### git log

查看当前分支的提交记录信息

### git reset

```
git reset [--soft | --mixed | --hard] [HEAD]
```

`HEAD` ：

- HEAD 表示当前版本
- HEAD^ 上一个版本
- `-soft`: 重置最新一次提交版本，不会修改你的暂存区和工作区。
- `-mixed`: 默认参数，用于重置暂存区的文件与上一次的提交(commit)保持一致，工作区文件内容保持不变。
- `-hard`: 重置所有提交到上一个版本，工作区改动清空。
- HEAD^^ 上上一个版本
- HEAD^^^ 上上上一个版本
- HEAD~n 回撤 n 个版本，这种也是更加方便的

**场景一（撤销 `git add` ）**

```
git reset` 或 `git reset HEAD
```

**场景二（撤销 `git commit` ）**

```
git reset HEAD^
```

**场景三**

回滚到上一个 commit 版本：

- `git log` 查看上一个 commit 记录，并复制 commitId
- `git reset --hard commitId` 直接回滚。

**场景四**

以前提交得代码被覆盖：

1. `git log` 找到有需要代码的那个 commitId
2. `git reset --hard commitId`
3. 复制代码
4. `git reflog` 查看 reset 前代码的 commitId
5. `git reset --hard commitId`
6. 粘贴代码

### git reflog

查看所有操作记录。

### git revert

`git revert [commitId]` 产生一次新的提交，提交的内容是帮你删掉你上次新增的内容

### git cherry-pick

如在 master 分支上改了原本其他分支需求的代码，并本地提交。可以先 `git log` 查看当前提交，找到 commitId 复制，然后切到目标分支，接着执行 `git cherry-pick master commitId1 commitId2 commitId4` 。

`git cherry-pick`：将执行分支的指定提交合并到当前分支

### git tag

**轻量标签：**`git tag v1.0.0`

创建一个只读的分支

**附注标签：**`git tag -a v1.0.1 -m "发布正式版 1.0.1"`

附注标签是存储在 Git 数据库中的一个完整对象， 它们是可以被校验的，其中包含打标签者的名字、电子邮件地址、日期时间， 此外还有一个标签信息，并且可以使用 GNU Privacy Guard （GPG）签名并验证。

打完标签之后，我们可以使用 `git show` 命令来看看这两种标签最终体现的信息有哪些。

**最后推送标签：**`git push origin tagName`

**其他命令：**

- 查看标签：`git tag`
- 筛选标签：`git tag -l v1.0.1`
- 删除标签：`git tag -d v1.0.1`
- 删除远程标签：`git push origin --delete v1.0.2`
- 另一种删除远程方式（表示将“:”前面空值替换到远程，也不失为一种方式）：`git push origin :refs/tags/v1.0.1`
- git rebase