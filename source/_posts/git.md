---
title: git
date: 2016-07-09 21:20:05
tags: git
categories: git
---

# git 的配置项  `git config` 以下的三个级别


###  global
针对一台电脑中一个用户，配置文件通常中用户的加目录的 .gitconfig 中
```bash

git config --配置级别  选项.属性 值 如：

git config --global color.ui true					＃配置ui颜色
git config --global user.name lhw                   ＃指定提交的用户名
git config --global user.email bjletaoba@sina.com   ＃指定提交的用户邮箱
git config --global core.editor emacs  (vim)        ＃指定打开的编辑器
git config --global merge.tool vimdiff              ＃指定比较工具
git config --list                                   ＃查看配置项
git help config                                     ＃帮助

```
### system
针对一台电脑，配置文件通常中/etc/.gitconfig 下

### local
针对每一个项目，配置文件通常中 每个项目的 .git/文件夹下

# 常用查看命令

`git status`
`git log --oneline`
`git log --oneline --graph`
`git  show  hash值` 查看状态和跟踪日志和hash 

### `git add` 用法

`git add` 将文件放入缓存区，其目的是为下次的 commit 做准备，其实每次 `git commit` 提交的文件都是 缓存区的，如果在缓存区的文件`commit`之前在次修改，那么修改的部分不会`commit`到版本控制中

```bash

git add . 		#添加所有文件
git add *.php 	# 添加PHP文件
git add /dir/* 	#添加目录

```

<!-- more -->

# git 使用总结

### 如何使`commit`尽可能的少

场景：通常一个commit就代表一个版本或者一个功能的提交，所以有时候我们不希望有过多的commit，比如已经`commit`到本地的lhw.php 文件，当我再次修改它的时候，我不希望这次的`commit` 纪录进log，这时可用使用`amend`：`git commit --amend`(会在指定的编辑器中打开，关闭后自动提交),注意只限于提交到本地并未推送到远端，如果已经推送到远端暂时没用想到办法。另外`amend`只适用于修正最后一次`commit`如果选择之前的某个版本，只能`git reset --hard`回到某个历史版本后在做`amend`。

查看修改可用使用： `git show 指定hash值` ，而此处 `git commit` 不会产生新的log (具体想要几个`commit`还要根据具体的场景)


### 追随回归到过去的某一个版本

`git reset --hard`  彻底回退到某个版本，本地的源码也会变为上一个版本的内容。

`git reset --soft`  回滚一个commit状态。能回到add之后那里,回退的是commit的信息，不会回退代码。如果还要提交，直接commit即可

### `gitignore` 忽略版本控制

一个简单的例子忽略编辑器生成的 .idea文件

`git rm -r  --cached .idea/`   ＃指目录，从缓存区中删除中添加

然后在 `gitignore` 配置中添加 `.idea` ,在提交。

### rebase 变基

由于分支上的commit合并到master的时候会产生新的commit,而rebase可以将分支上提交整合到master上，执行步骤如下：

`git checkout feacture` 切换到发布分支

`git rebase master` 已master为基础

`git checkout master` 切回主干

`git merge feacture` 合并发布分支

### cherry-pick 挑拣commit

场景：假设我们有个稳定版本的分支，叫v2.0，另外还有个开发版本的分支v3.0，我们不能直接把两个分支合并，这样会导致稳定版本混乱，但是又想增加一个v3.0中的功能到v2.0中，这里就可以使用cherry-pick了。

如把dev分支上的某些commit合并到release上

`git checkout dev` 当前的开发分支上

`git checkout 0628fadz` 从当前开发分支的0628fadz这个点开始检出代码

`git branch test`  为检出的代码创建test分支

`git checkout test` 切换test分支

`git checkout release` 切换到要合到的分支上

`git cherry-pick 014de66 4dd1669 b6493af 803d021` 将要合并的commit按先后顺序合并过来

`git check-pick --abord` 如果遇到冲突可以取消操作并返回到预序列状态

`git status`

`git check-pick --abord` 可以连续使用

`git push -u origin test` 

`git checekout release`

`git merge test`

`git pull`

`git push`

具体使用参照 ： https://git-scm.com/docs/git-cherry-pick

# 分支

分支的使用场景：

分支使用场景个人理解也就是git的开发规范，git开发规范有很多，如gitflow,感觉gitflow确实解决了许多问题，但同时又带出来了许多新的问题，如分支的最终管理最终看起来很乱。

简单的一套git分支规范：master(受保护的) ，dev(开发分支)，feacture(欲发布)，开发人员拉取dev分支在本地小步提交，开发完成合并到feacture，在由feacture合并到master

大范围的bug,可以创建一个bug分支.


新建分支：

`git branch -b  lhwtest_1000`

切换分支：

`git checkout  -b lhwtest_1000`

查看分支：

`git branch`


提交到分支：

`git commit -m 'new touch file'`

推送到指定 master 分支:

`git push origin lhwtest_1000`

切换到主干：

`git checkout master`

合并分支：

`git merge origin lhwtest_1000`

最后推送到master:

`git push origin`

单独revest某个文件 ： 

`git checkout -- /app/lhw.php `

分支开发完成没有bug后删除分支： 

`git branch -d lhwtest_1000` 

注意分支开发过程中要不断的获取主干代码，保持本地与主干的代码同步, 获取最新的远端代码(否则无法推送到远端)，已保证远端代码和本地是否存在冲突

`git fetch  origin`

`git pull  origin`

pull 于 fetch 的区别：

pull = fetch + merge , git fetch 的时候会从远端拉取一个隐藏master分支,使用git branch -r 查看，这个分支会与本地分支进行diff,然后决定是否需要合并，而 git pull 则直接进行合并

通常都说 git fetch 更加安全，但是实际工作中都默认使用 git pull 因为我们默认就是要已远端代码为准。




 # 冲突解决

 冲突场景：当在分支和主干修改并提交了相同文件的相同位置，在与主干合并时`git`会提示`merge failed fix conficts and then commit the result`,此时可以通过 `git diff file`查看文件冲突信息，并且用编辑器打开文件修改冲突信息，保存退出，重新 `git add`和 `git commit`

 # git 命令别名

 一 git 本身的配置文件

 添加：`git config --global alias.s status` 
 删除：`git config --global --unset alias.s`

二 linux `bash shell` 的 `.bash_profile` 或者 `zsh`的 `.zshrc`

```bash
alias ll='ls -al'
alias subl="'/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl'"
alias nano="subl"
alias des="cd ~/WebServer/Documents/"
alias gs="git status"
alias gc="git commit -m "
alias gaa="git add ."
alias gp="git push"
alias gl="git log --graph"
alias he="cd ~/hexo/hexo/lhw-hexo/"
alias pos="cd ~/hexo/hexo/lhw-hexo/source/_posts/"
export EDITOR="subl"
```
# github 常用技巧

快捷键 ： shift + ?

查看一些流行的packgecg : https://github.com/trending

pull代码的时候，经常会和本地有合并情况，一但合并便会产生新的分支，使分支线比较凌乱，解决办法：

命令行：git pull --rebase

phpstorm  选择rebase

sourcetree 勾选寅生选项

# 理解一些感念

一个本地仓库可以对应多个远程仓库，clone下来的仓库默认是与远端origin建立关联的,所以push的时候是可以直接push的，
使用 remote add 指向新的远程仓库。具体学习githubflow的流程，所以在push一个没用和远端建立关联的本地仓库时通常是要 push -u 指定远程仓库名称的。