---
title: Git
categories:
  - Linux
tags:
  - Git
cover: https://image.runtimes.cc/202404051527622.jpg
abbrlink: 25246
date: 2022-11-19 18:25:00
updated: 2023-11-07 17:28:27
---

# 分支管理
## 切一个新的本地分支
```shell
# 先切换到需要复制的分支,比如想从prod分支,切一个新的分支
git checkout prod   # 先到prod分支
git checkout -b prod-fix  # 从prod分支拷贝一个新的分支叫prod-fix
```

## 

# 用命令pull request代码

```
# 把本地的代码切换到master
git checkout -b theme-next-master master
# https://github.com/theme-next/hexo-theme-next.git 是要合并的代码
git pull https://github.com/theme-next/hexo-theme-next.git master
```

# Git Key

```
cd ~/.ssh
ssh-keygen
cat ~/.ssh/id_rsa.pub
```

# Git Bash启动慢

```
git config --global core.preloadindex true
git config --global core.fscache true
git config --global gc.auto 256
```

# 推送到远程

```
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:YangAnLin/bbs.git
git push -u origin master
```

# 创建新的分支推送到远程

1. 先创建本地分支`git checkout -b <branch-name>`
2. 本地分支推送到远程服务器时，远程分支自动创建，推送本地分支到远程

```
git push --set-upstream <remote-name> <local-branch-name>:<remote-branch-name>

<remote-name>：远程git服务器名称，一般设为origin
<local-branch-name>：本地分支名称
<remote-branch-name>：远程分支名称

最后的结果是: git push --set-upstream origin prerelease:prerelease
prerelease是分支的名字

也可以这样操作:本地有个master_anthony,服务器中没有这个分支,
git push -u origin master_anthony
这个命令,就是把master_anthony分支的代码,提交到远程,还要在远程创建这个分支
```

# 删除远程仓库文件

```bash
# 加上 -n 这个参数，执行命令时，是不会删除任何文件，而是展示此命令要删除的文件列表预览。
git rm -r -n --cached 文件/文件夹名称

git rm -r --cached 文件/文件夹名称

git commit -m "提交说明"

git push
```

# 撤销修改

```
# 工作区修改过了,恢复工作区
git checkout -- file

# 已经commit了,但是还没有push
git reset --soft HEAD^

# 现在就已经把暂存区恢复到工作了
```

# 版本回退reset

reset用于回退版本，可以遗弃不再使用的提交

```prolog
# 查看日志提交log
$ git log --pretty=oneline
c27561bbe42dc1ae1b08442ce0d8100d4e02a689 (HEAD -> master) stage
1fd6bc87438d26b24c93441b82c8840a9ad47e32 test_3
66f444c9de0b3c2d1cddb8f17becca7ce774a7c7 test_2
2502c400cab4d7cb48ed3e4200577f428904da9b test_1
26d73f758411c3656b7fa243d77837943a6c782c 321321
5fa733378d5eb187d1d46bf2d2395e6c67867a8d 测试
e9fa63e3bafc275e99cc88b40cc68bde6933323c 123
3d338b67e2b57dcd6887099cb3977f5d96b3cbb5 第一次提交

# 远程地址回退版本
# 先让本地回退到指定版本
git reset --hard HEAD^
#再推送到远程(这样也会把别人提交的给弄消息了)
git push --force

# 操作记录
$ git reflog
c27561b (HEAD -> master) HEAD@{0}: commit: stage
1fd6bc8 HEAD@{1}: reset: moving to 1fd6bc8
66f444c HEAD@{2}: reset: moving to HEAD^
1fd6bc8 HEAD@{3}: commit: test_3
66f444c HEAD@{4}: commit: test_2
2502c40 HEAD@{5}: commit: test_1
26d73f7 HEAD@{6}: commit: 321321
5fa7333 HEAD@{7}: commit: 测试
e9fa63e HEAD@{8}: commit: 123
3d338b6 HEAD@{9}: commit (initial): 第一次提交
```

# 版本回退revert
![](https://image.runtimes.cc/202404051416064.png)

```yaml
git reset <ID>
git push
```

📌 操作同一个文件的时候,容易冲突


# 分支管理

```Shell
# 查看提交历史
git log --graph --pretty=oneline

# 删除本地分支
git branch -d localBranchName

# 删除远程分支
git push origin --delete remoteBranchName

# 设置全局默认的分支名字
git config --global init.defaultBranch <名称>

# 修改分支名字
git branch -m <name>
```

# TAG管理

查看本地所有的tag

```
git tag
```

新建tag

```
# 在本地创建一个tag
git tag v2.0

# 把本地所有tag推送到远程
git push --tags

# 把指定的tag推送到远程
git push origin [tagname]
```

# Git子模块

## 克隆项目指定分支

```
git clone -b prod https://git.oschina.net/oschina/android-app.git
```

## 克隆项目

第一种方式:

```
先clone父项目
git clone sum.git

再初始化子项目
git submodule init
git submodule foreach git pull

再更新子项目
git submodule update
```

第二中方法

```
# 克隆完整的项目
git clone git@github.com:jjz/pod-project.git --recursive
```

## 添加项目

```
git submodule add module1.git
```

# Rebase

```
# 进入编辑模式
git rebase -i N
```

这些 commit 自旧到新由上而下排列

在合并 commit 这个需求里，我们可以选择 pick(p) 最旧的 commit1，然后在后续的 commit_id 前添加 squash(s) 命令，将这些 commits 都合并到最旧的 commit1 上。

保存 rebase 结果后，再编辑 commit 信息，使这次 rebase 失效，git 会将之前的这些 commit 都删除，并将其更改合并为一个新的 commit5

提交的过程中,可能需要加 `-f` 强制推送

操作过程中,遇到出错,使用

```
git rebase --abort/--continue/--edit-todo
```

# 要注意的

- 进入到子项目的目录中,默认的分支并不是master,需要手动切换到master
- 删除子模块的,没有这样的功能,需要手动修改`.gitmodules`文件
- 添加了新的子模块之后,`.gitmodules`就会被创建或者是修改,这个文件需要推送到远程仓库

# 储藏

比如在当前分支正在开发,突然来了一个bug,但是当前写的代码又不能提交,需要先储藏起来

```
 git stash
```

等bug改好,再回来

```
git stash pop
```

# 升级

window

```
git update-git-for-windows
```

# Github更新fork别人的项目

1.打开自己的仓库，进入code下面

2.点击new pull request创建
![](https://image.runtimes.cc/202404051418900.png)

# 终端配置github person token
应用场景 Github Personal Access Token in Jenkins
```bash
# 在命令行中的用户主目录创建如下文件
vi .git-credentials

# 写入如下内容，请注意将 {username}, {password} 替换成自己的 github 用户名和 token
https://{username}:{password}@github.com

# 退出保存,执行
git config --global credential.helper store
```

# SourceTree
## 各种状态
1.添加文件或者修改文件
uncommitted changes 这次是添加了`f.txt`文件
![未提交](https://image.runtimes.cc/202404051418039.png)

2.add的状态
![add](https://image.runtimes.cc/202404051418540.png)

3.提交的状态
这里`超前一个版本`,是相对远程
![提交](https://image.runtimes.cc/202404051418125.png)

4.推送完的状态
![提交](https://image.runtimes.cc/202404051418448.png)

## rebase分支
1.这里的分割线的这次提交是用来分区之前的测试,表示新的测试,测试rebase分支,相当于原始的样子
![原始状态](https://image.runtimes.cc/202404051418043.png)

2.如果现在想要开发一个新的功能
先在远程的main分支上右键,检出一个自己的分支`dev`
![](https://image.runtimes.cc/202404051419575.png)
![](https://image.runtimes.cc/202404051518534.png)
![](https://image.runtimes.cc/202404051419385.png)

3.在dev分支上新建/或者编辑文件,我这里创建的一个文件,叫`dev分支创建的文件.txt`,然后提交到本地,`不要推送`
![](https://image.runtimes.cc/202404051419086.png)
这里的`超前1个版本`,是相对于`origin/main`来说的

4.再切换到main分支
1. 我这里创建了一个文件,叫`master分支创建的文件.txt`,提交并且推送到远程
(这个操作相当于,你在dev分支 开发自己的功能,然后有别的同事提交了新的代码提交到`main`分支)

2. pull main分支的代码

5.再切换的dev分支,选中main分支,右键,选择将当前变更变基到main
![](https://image.runtimes.cc/202404051420630.png)

这里的: 
* 超前1个版本: 相当于本地的dev分支来说
* 落后一个版本: 相对于远程分支来说

如果有冲突则合并冲突，点击左上角(中间位置)的提交，选择继续变基
* 1.第三步 的操作,已经提交到本地了
* 2.如果这里解决冲突,就相当于,有编辑了一次文件
* 3.再把编辑的提交一次到本地

6.此时我们的本地更新是基于最新的 main 分支
![](https://image.runtimes.cc/202404051420367.png)
点击推送,把dev分支,推送到远程
![](https://image.runtimes.cc/202404051420877.png)

7.切换到main分支,点拉取,拉取dev分支到main分支
![](https://image.runtimes.cc/202404051420776.png)
![](https://image.runtimes.cc/202404051420936.png)

再推送main
![](https://image.runtimes.cc/202404051420518.png)
![](https://image.runtimes.cc/202404051420688.png)