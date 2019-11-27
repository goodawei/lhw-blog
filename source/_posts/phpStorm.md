---
title: phpStrom
date: 2016-06-04 23:10:45
tags: phpstrom
categories: 编辑器
---

### idea 

http://idea.lanyus.com/

### phpStrom 常用快捷键：

`mac`下

command + ，打开配置项

command + shift + a 列出所有通过 keymap 定义快捷键

command + 1 隐藏和显示左侧工具栏

command + e 最近打开的文件

command + shift + o 搜索文件其实不用总是在左侧的工具栏中找，使用快捷键效率更高

command + shift + F 全局搜索

command + 7 显示类中的方法，右键可以将显示区域，移动到 左，上 右 不同区域

command + l 跳转指定的行数

command + x 删除当前行

command + d 复制当前行

command + b 跟踪变量至声明处

command + option + l 格式化代码

command + w 关闭当前窗口

command + option + / 多行注释

command + 6   查找//@todo标签


常用工具条的显示和隐藏在view下编辑。 一些不常用的可以隐藏掉。

### 多点编辑

鼠标方式：按住 `option` ＋ 鼠标左键选中编辑内容

快捷键 ：control + g  和  command ＋ control + g

### 更改keymap 重置快捷键（按快捷键搜索）

### live templates


### 主题配置：

常规配置流程 command + ，呼出配置项 或者 `preferences` 进入 `font` 选择 `scheme`配置项 .
<!-- more -->
如果选择字体`primaryfont`，字体大小`size`或者间距`linespacing`的话，首先要`save as` 一份`scheme `.

### 自定义主题：

`daylerees.github.io `上选择一款自己喜欢的主题： 如选择 `Potpourri`，然后去
`https://github.com/daylerees/colour-schemes/tree/master/jetbrains`找到对应的主题文件如图：

![](http://o8979n2hu.bkt.clouddn.com/choose_theme.png)

进入选择具体的URL

![](http://o8979n2hu.bkt.clouddn.com/choose_theme_url.png)

进入命令行

![](http://o8979n2hu.bkt.clouddn.com/choose_theme_terminal.png)

```bash
wget https://raw.githubusercontent.com/daylerees/colour-schemes/master/jetbrains/potpourri.icls
```
下载后重启 phpstrom


----------

或者去 `http://www.ideacolorthemes.org/themes/` 上选择喜欢的主题然后下载. 

![](http://o8979n2hu.bkt.clouddn.com/choose_theme_down.png)

然后`file`倒入设置

![](http://o8979n2hu.bkt.clouddn.com/import.png)


----------

将主题和左侧的工具栏色调保持一致方法：
`command +  ，`呼出配置项,然后搜索`plugins`,下载 `color Ide`

![](http://o8979n2hu.bkt.clouddn.com/silbal_color.png)

![](http://o8979n2hu.bkt.clouddn.com/coloride_install.png)

将主题字体和左侧的工具栏字体保持一致方法：

![](http://o8979n2hu.bkt.clouddn.com/font.png)
----------

下载字体（ primary font） 方法：

以FiraCode字体为例

登陆 `https://github.com/tonsky/FiraCode`  下载 然后 倒入设置


### phpstrom 极简主义配置

一 隐藏各种 toolbar

view 去掉 toolbar ，status Bar , Navigation Bar ,Tool Buttons 勾选

二 隐藏 breadcrumbs 

呼出配置项，搜索 breadcrumbs ，去掉 show breadcrumbs 勾选项

三 隐藏文件名，保持当前窗口直有一个文件

文件右键，选择 tabs placement ,选择 none 

恢复，呼出配置项 搜索 editor tabs 重新指定 placement 属性

四 配置主题

去选主题 http://daylerees.github.io

下载主题 https://github.com/daylerees/colour-schemes 选择 jetbrains ，具体参数以上步骤。

```php
cd ~/Library/Preferences/WebIde100/colors

wget https://raw.githubusercontent.com/daylerees/colour-schemes/master/jetbrains/earthsong.icls
```

重启 phpstrom，呼出配置项，选择主题 ，save as 为 earthsong-theme，在重新设置字体等。

五 统一 left bar 的颜色

快捷键 command + shift + A  搜索 plugins 然后选择 config plugins，或者 呼出配置项 搜索 plugins,然后选择 Browse repositores ... , 搜索color ide ,安装


六 两个重要的快捷键重置

（1）搜索文件 改为 command + P

 command + shift + A  搜索 keymap 进入设置项页面，首先将default  copy一份后在重置，点击copy ,命名 lihongwei-keymap 

 搜索 command + shift + O , 然后 remove  command + shift + O ,在 add keyboard shortcut ,输入新的快捷键，点 ok 

 (2) 列出文件中的方法原快捷键 command + F12 改为 command + W

 	方法同上,注意 F12 同时要按住 Fn


七 快速创建文件

	command + 上箭头 产看当前文件路径，可以通过左右箭头控制要创建文件所要在的目录然后，在按 command + N 调出新建文件选项框

八 模版

	连续两下 shift 搜索 file template  或者 command + shift + A  搜索 file template

	(1) 新建模版名为 Eloquent Model ，Extension 为 php 

```php

#parse("PHP File Header.php")

#if (${NAMESPACE})

namespace ${NAMESPACE};

#end

class ${NAME} extends Eloquent {
    protected \$fillable = [];
}

```
	然后去新建 Eloquent Model

九 动态模版



十 关于构造，set,get,doc

	创建构造方法,设置属性值 ,获取属性值 ，文档注释 command + N 选择对应的选项

	给构造方法做依赖注入快捷键 option + return (回车) 然后可以指定要构造的属性 ，前提条件，鼠标要在构造方法的括号内

	构造的动态模版,搜索 live template ,选择 php ,新建 _c

```php

public function __construct($ARGS$){
    $END$
}

```

最后difine PHP

使用 直接 _c ，然后 option + return





