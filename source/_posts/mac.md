---
title: mac 开发大杂烩
date: 2016-11-21 20:42:26
tags: mac
categories: mac
---

![iterm2][1]

[1]: http://o8979n2hu.bkt.clouddn.com/iterm-theme.png

# :eyes: mac终端常用配置

### 安装oh my zsh

首先安装 `iterm2`  http://www.iterm2.com

然后进入 http://ohmyz.sh 根据以下文档安装 `zshrc`

```php
	git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh

	cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```
配置

`zsh` 的配置主要集中在用户当前目录的`.zshrc`里，用 `vim` 或你喜欢的其他编辑器打开`.zshrc`：

```bash
alias cls='clear'
alias ll='ls -l'
alias la='ls -a'
alias vi='vim'
alias javac="javac -J-Dfile.encoding=utf8"
alias grep="grep --color=auto"
alias -s html=mate   # 在命令行直接输入后缀为 html 的文件名，会在 TextMate 中打开
alias -s rb=mate     # 在命令行直接输入 ruby 文件，会在 TextMate 中打开
alias -s py=vi       # 在命令行直接输入 python 文件，会用 vim 中打开，以下类似
alias -s js=vi
alias -s c=vi
alias -s java=vi
alias -s txt=vi
alias -s gz='tar -xzvf'
alias -s tgz='tar -xzvf'
alias -s zip='unzip'
alias -s bz2='tar -xjvf'

```

<!-- more -->

安装完成，终端输入`zsh`进入 zsh



### 安装 `zsh-autosuggestions`

```php
	git clone git://github.com/zsh-users/zsh-autosuggestions ~/.zsh/zsh-autosuggestions
```
在 `.zshrc` 添加：

```php
	source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh
```
最后 `source ~/.zshrc`

更多 `oh my zsh` 配置参考：

https://zhuanlan.zhihu.com/p/19556676



### iterm2 的配置

zsh的主题 ： sudo vi ~/.zshrc  下的 ZSH_THEME="cloud"

iterm2的主题：

https://github.com/mbadolato/iTerm2-Color-Schemes

https://draculatheme.com  , Dracula.itermcolors (非常不错哦)

步骤：clone 选好的配色，command + , 呼出配置项 新建 profiles ,选择 `Colors` 选项导入配色文件,导入成功后，还要在 iTerm2 - Preferences - Profiles - Colors 里面右下方的 Color Presets 里面有没有刚刚导入的主题如图。

![iterm2-theme][2]

[2]: http://o8979n2hu.bkt.clouddn.com/iterm2.png


然后 command + o 可以选择配色实用了

![iterm2-theme][3]

[3]: http://o8979n2hu.bkt.clouddn.com/iterm-mac.png

经常使用的快捷键

command + o 选择主题配色

command ＋ 左右箭头 来回切换

command + enter  切换全屏

command + ;  查看历史命令

command + shift + h  查看剪贴板历史

ctrl + u  清除当前行(废除当前命令)

ctrl + r  搜索命令历史

ctrl + d 删除当前光标的字符

ctrl + h  删除光标之前的字符

ctrl + w  删除光标之前的单词

ctrl + k  删除到文本末尾

ctrl + t 交换光标处文本

command + r 清屏1

ctrl + l  清屏2
