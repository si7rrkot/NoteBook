# git常用操作

## 常用git命令

| git tag                                    | 列出所有的tag信息                                            |
| ------------------------------------------ | ------------------------------------------------------------ |
| git tag v0.0.1                             | 创建一个名为v0.0.1的tag                                      |
| git push origin v0.0.1                     | 推送tag到远程仓库                                            |
| git tag -d v0.0.1                          | 删除名为v0.0.1的tag                                          |
| git push origin --tag                      | 推送所有tag到远程仓库                                        |
| git push origin --delete <BranchName>      | 删除远端分支 xxx                                             |
| git rm --cached <BranchName>               | 放弃对文件xxx的跟踪,并保留在本地                             |
| git rm --f <BranchName>                    | 删除xxx的跟踪，并且删除本地文件                              |
| git branch -d <BranchName>                 | 删除本地分支                                                 |
| git config core.filemode false             | git中忽略文件权限或文件拥有者的改变                          |
| git config --global core.fileMode false    | 全局设置git中忽略文件权限或文件拥有者的改变                  |
| git config --global core.quotepath false   | 设置不转义中文字符(命令行或bash运行git命令时显示的文件路径不用编码模式显示) |
| git log <last release> HEAD --grep feature | 查看自<last release>以来的所有feature的提交                  |



## 1.分支重命名

**注：**想要修改分支名，我们需要将远端的分支拉下来，重新修改。

旧分支：oldBranch

新分支：newBranch

<br/>

**步骤：**

1. 先拉一下仓库，保证最新。
   ```powershell
   git pull
   ```
2. 先将本地分支重命名。
   ```powershell
   git branch -m oldBranch newBranch
   ```
3. 删除远端分支（如果分支尚未传到远端，则可以跳过该步骤）
   ```powershell
   git push --delete origin oldBranch
   ```
4. 将重命名之后的分支推送到远端
   ```powershell
   git push origin newBranch
   ```
5. 把修改后的本地分支和远程分支关联
   ```powershell
   git branch --set-upstream-to=origin/newBranch
   ```

## 2.一些常用的配置

#### 中文乱码

```powershell
git config --global core.quotepath false
```

#### 配置用户名和邮箱

```powershell
git config --global user.name "xxx"
git config --global user.email "xxxx@mail"
```

## 3.恢复git被删除的本地分支

1. 查看操作记录
   ```powershell
   git reflog show --date=iso
   ```
2. 找到对应分支最后一个提交记录，复制第一个7位的字符串
3. 恢复分支
   ```powershell
   git checkout -b xxxx 1234567
   ```

## 4.git stash

`git stash`将变化储存的工作目录中

```shell
git stash list [<options>]
git stash show [<stash>]
git stash drop [-q|--quiet] [<stash>]
git stash ( pop | apply ) [--index] [-q|--quiet] [<stash>]
git stash branch <branchname> [<stash>]
git stash save [-p|--patch] [-k|--[no-]keep-index] [-q|--quiet]
             [-u|--include-untracked] [-a|--all] [<message>]
git stash [push [-p|--patch] [-k|--[no-]keep-index] [-q|--quiet]
             [-u|--include-untracked] [-a|--all] [-m|--message <message>]]
             [--] [<pathspec>…]]
git stash clear
git stash create [<message>]
git stash store [-m|--message <message>] [-q|--quiet] <commit>
```

使用`git stash`时要录制工作目录和索引的当前状态，但想回到干净的工作目录。该命令保存您的本地修改，并恢复工作目录以匹配`HEAD`提交。

通过此命令隐藏的修改可以使用`git stash list`，检查`git stash show`和恢复（可能在不同的提交之上）`git stash apply`。`git stash`没有任何参数的调用相当于`git stash save`。默认情况下，存储列为“WIP on `branchname`...”，但在创建存储时，可以在命令行上提供更多描述性消息。

您创建的最新储藏器存储在`refs/stash`; 旧的存储在这个引用的reflog中可以找到，并且可以使用通常的reflog语法来命名（例如`stash@{0}`，最近创建的存储，`stash@{1}`是之前的存储，`stash@{2.hours.ago}`也是可能的）。通过指定存储索引也可以引用Stache（例如，整数`n`等价于`stash@{n}`）。





**git stash**
保存当前工作进度，会把暂存区和工作区的改动保存起来。执行完这个命令后，在运行git status命令，就会发现当前是一个干净的工作区，没有任何改动。使用git stash save 'message...'可以添加一些注释

**git stash list**
显示保存进度的列表。也就意味着，git stash命令可以多次执行。

**git stash pop [–index] [stash_id]**
git stash pop 恢复最新的进度到工作区。git默认会把工作区和暂存区的改动都恢复到工作区。
git stash pop --index 恢复最新的进度到工作区和暂存区。（尝试将原来暂存区的改动还恢复到暂存区）
git stash pop stash@{1}恢复指定的进度到工作区。stash_id是通过git stash list命令得到的
通过git stash pop命令恢复进度后，会删除当前进度。
**git stash apply [–index] [stash_id]**
除了不删除恢复的进度之外，其余和git stash pop 命令一样。

**git stash drop [stash_id]**
删除一个存储的进度。如果不指定stash_id，则默认删除最新的存储进度。

**git stash clear**
删除所有存储的进度。
