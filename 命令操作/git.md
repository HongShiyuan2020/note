
## 几个区域

![](Pasted%20image%2020250306211936.png "Git 各个区域与操作")
### 工作区（Workspace）

- 使用文本编辑器的交互区域，即当前可编辑的文本，代码等数据。
### 暂存区（Index）
- 使用`git add`指令后会将工作区的文件保存到该区域。
### 仓库区（Repository）
- 保存提交的更新，使用`git commit`后将暂存区的数据保存在该区。
### 远程区（Remote）
- 远程仓库，如github中的仓库，使用远程指令与其进行交互。

## HEAD 指针

1. HEAD和BRANCH指针本质是指向某个**commit对象**的指针；
2. BRANCH指针指向的是**BRANCH所在分支**最新提交产生的commit对象；
3. HEAD指向的是某一个**当前所使用的分支最新提交**产生的commit对象；

## 一. 远程操作
### git remote 

```shell
git remote                             # 列出远程仓库
git remote -v                          # 列出远程仓库，包括地址
git remote show <主机名>                # 显示主机详细信息
git remote add <主机名> <仓库地址>
git remote rm  <主机名>
git remote rename <原主机名> <新主机名>
```
### git clone

- `git clone` 将远程仓库克隆到本地，**包括被clone仓库的版本变化**。(无需 `git init`)
```shell
git clone <版本库的网址>                 # 从远程仓库克隆整个版本信息和文件
git clone <版本库的网址> <本地目录名>
```

### git fetch

- 如果没有显式的指定远程分支, 则远程分支的master将作为默认的FETCH_HEAD
- 如果指定了远程分支, 就将这个远程分支作为FETCH_HEAD
- `git fetch`并不会进行merge操作
```shell
git fetch <远程仓库> <远程分支>:<本地分支>
git fetch                                # 默认取回所有分支的最新commit
```

### git pull

- 从远程获取指定分支的最新commit，并将其merge到指定分支的最新commit中
- `git pull` = `git fetch` + `git merge`

```shell
git pull <远程主机名> <远程分支名>:<本地分支名>
```

- 如果当前分支与远程分支存在追踪关系，`git pull`就可以省略远程分支名。
```shell
git branch --set-upstream master origin/next
git pull origin             # 默认将master分支push到远程next分支
```

### git push

- 命令用于将本地分支的更新，推送到远程主机。它的格式与`git pull`命令相仿。

```shell
git push <远程主机名> <本地分支名>:<远程分支名>
git push <远程仓库> :<远程分支>                     # 
git push --all origin                            # push 所有分支到远程
```

## 二. 分支管理

### git branch

```shell
# 列举分支
git branch                  # 列出所有本地分支
git branch -r               # 列出所有远程分支
git branch -a               # 列出所有分支

# 添加分支
git branch <branch-name>    # 新建分支但仍然保持当前分支
git branch <branch-name> <commit> # 新建分支，且指定所基于的commit
git branch --track <branch-name> <remote-branch> # 新建分支，追踪远程分支

# 删除分支
git branch -d <branch-name>
git branch -dr <remote-host>/<branch> # 删除远程分支

# 其他
git branch --set-upstream <branch-name> <remote-branch>
```

### git checkout

```shell
# 切换分支
git checkout <branch-name>
git checkout -b <branch-name>   # 切换时如果分支不存在会新建分支
git checkout -                  # 切换到上一个分支

# 撤销
git checkout <file-name>        # 恢复暂存区的某个文件到工作区
git checkout <commit-name> <file-name>        # 恢复指定commit的某个文件到工作区和暂存区
git checkout .                                # 恢复暂存区的所有文件到工作区
```
### git rebase

#### 变基执行过程

- 首先，`git` 会把 `feature1` 分支里面的每个 `commit` 取消掉；  
- 其次，把上面的操作临时保存成 `patch` 文件，存在 `.git/rebase` 目录下；  
- 然后，把 `feature1` 分支更新到最新的 `master` 分支；  
- 最后，把上面保存的 `patch` 文件应用到 `feature1` 分支上；

```shell
git rebase <branch-name>     # 把当前分支变基到指定分支下
git -i rebase <branch-name>  # 交互模式下进行操作
```

### git merge

```shell
git merge <branch-name>     # 把当前分支和指定分支合并
```
## 状态查看指令
### git  log

```shell
git log                       # 显示当前分支的历史纪录
git log --stat                # 显示当前分支的历史记录，包括发生变动的文件
git log -S <keyword>          # 更具关键词搜索
```

### git diff

```shell
git diff                      # 显示暂存区和工作区的差异
git diff --cached <file>      # 显示暂存区和上一个commit的差异
git diff HEAD                 # 显示工作区和HEAD指向的commit之间的差异
```

### git status

```shell
git status                   # 显示有变更的文件
```