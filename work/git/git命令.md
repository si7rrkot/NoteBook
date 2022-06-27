# git命令

## 常用git命令

| git tag                                 | 列出所有的tag信息                           |
| --------------------------------------- | ------------------------------------------- |
| git tag v0.0.1                          | 创建一个名为v0.0.1的tag                     |
| git push origin v0.0.1                  | 推送tag到远程仓库                           |
| git tag -d v0.0.1                       | 删除名为v0.0.1的tag                         |
| git push origin --tag                   | 推送所有tag到远程仓库                       |
| git push origin --delete <BranchName>   | 删除远端分支 xxx                            |
| git rm --cached <BranchName>            | 放弃对文件xxx的跟踪,并保留在本地            |
| git rm --f <BranchName>                 | 删除xxx的跟踪，并且删除本地文件             |
| git branch -d <BranchName>              | 删除本地分支                                |
| git config core.filemode false          | git中忽略文件权限或文件拥有者的改变         |
| git config --global core.fileMode false | 全局设置git中忽略文件权限或文件拥有者的改变 |