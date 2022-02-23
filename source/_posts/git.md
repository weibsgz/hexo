---
layout: post
title:  git 常用命令解析
date:   2021-11-06 01:08:00 +0800
categories: 工具
tag: 工具
---

### 撤回方案 git reset

1. reset 命令的原理是根据 commitId 来恢复版本。因为每次提交都会生成一个 commitId，所以说 reset 可以帮你恢复到历史的任何一个版本。
- 比如，要撤回到某一次提交，命令是这样：
`$ git reset --hard cc7b5be`
- `git log` 命令查看提交记录，可以看到 commitId 值, 这个值很长，我们取前 7 位即可。

2. 比如你已经commit过了 这时候可能又不想commit了 比如你想修改一下提交的信息

3.  比如说当前提交，你已经推送到了远程仓库；现在你用 reset 撤回了一次提交，此时本地 git 仓库要落后于远程仓库一个版本。此时你再 push，远程仓库会拒绝，要求你先 pull.如果你需要远程仓库也后退版本，就需要 -f 参数，强制推送，这时本地代码会覆盖远程代码。


`git reset HEAD^` 

- HEAD^ 表示上一个提交，可多次使用。之后再重新 `git add . git commit -m '....'`

### git revert

- revert 与 reset 的作用一样，都是恢复版本，但是它们两的实现方式不同。
- 简单来说，reset 直接恢复到上一个提交，工作区代码自然也是上一个提交的代码；而 revert 是新增一个提交，但是这个提交是使用上一个提交的代码。

- 因此，它们两恢复后的代码是一致的，区别是一个新增提交（revert），一个回退提交（reset）。

- 正因为 revert 永远是在新增提交，因此本地仓库版本永远不可能落后于远程仓库，可以直接推送到远程仓库，故而解决了 reset 后推送需要加 -f 参数的问题，提高了安全性。

- 说完了原理，我们再看一下使用方法：
`git revert -n [commitId]`
