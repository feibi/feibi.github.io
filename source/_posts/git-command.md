---
title: Git常用命令
date: 2019-12-19
tags: Git
categories: Git
---

##### 分支比较
```git
git diff  

option:   
--name-only 	只比较文件名
--stat		比较统计信息

a). git diff <branch1>..<branch2>  	比较两个分支之间差异 
b). git diff --staged					比较暂存区和版本库差异
```



##### 管理源
```git
git remote -v 查看远程源
git remote show origin  查看remote地址，远程分支，还有本地分支与之相对应关系等信息
git remote rm origin			删除源
git remote add origin <url>		添加源
```

##### 设置分支远程与本地的对应关系
```git
git branch -u origin/<branch> <branch>
```
##### 删除远程分支
```git
git push origin --delete <branch> 
git remote prune origin  删除远程仓库不存在的分支
```

##### 回退到<commit id>的版本
```git
git  reset --hard  <commit ID>
```

##### 撤消上一个commit，但保留add的文件
```git
git reset --soft HEAD~1
```

##### 撤销add内容
```git
git reset head
```

##### 清除untracked files
```git
git clean -f -d
```

##### 合并commit id
```
git cherry-pick <commit id>
```

##### 免密登陆
```git
git config --global credential.helper store
```

##### 提交所有标签
```git
git push origin --tags
```

##### 更新version
```
npm version [<newversion> | major | minor | patch | premajor | preminor | prepatch | prerelease | from-git]
```

##### 删除远程标签
```git
git push origin --delete tag <tagName> 
```

##### 查看git stash pop操作
```git
git fsck --no-reflog
```

##### 恢复因git reset --hard导致丢失的工作区文件

```
 find .git/objects -type f | xargs ls -lt | sed 20q
 
 //.git/objects/1f/b72f8f5e711cf3c6e968942bf70c7668e05d75
```

> 注意去掉分隔符 `git cat-file -p 1fb72f8f5e711cf3c6e968942bf70c7668e05d75`