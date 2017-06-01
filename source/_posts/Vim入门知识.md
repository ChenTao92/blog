---
title: 关于Vim入门的一些小知识以及操作符
date: 2016-10-12 10:13:41
categories: front End
tags:
 - Vim
---

### Vim是什么？
 >Vim - the ubiquitous text editor
Vim is a highly configurable text editor built to make creating and changing any kind of text very efficient. It is included as "vi" with most UNIX systems and with Apple OS X.

### 如何使用Vim?
打开终端，输入vim，便可以运行vim编辑器。打开Vim后直接默认进入普通模式，在普通模式下，每一个按键有独特的含义（按下a并不会输入a这个字符），所以这个时候你按键是不会有反应的，按下小i键(insert)或者小a键(append)就可以进入插入模式，这时每个按键就变成了相应字符的输入。按下ESC就可以返回普通模式。
<!-- more -->
### 普通模式下操作符的使用。
Vim强大的编辑能力就来源于其普通模式命令。学会了这些操作符并且能灵活组合运用的话就可以更加高效的进行文本编辑。接下来就介绍一下实用的指令吧。如果是使用Sublime作为编程工具的话也可以使用一下指令哦。

- 简单的移动
    `h`:左
    `j`:下
    `k`:上
    `l`:右
使用这些操作符就再也不用在字母键和箭头键之间来回切换啦。
`b `(back/光标向前移动一个单词)
`0` (光标移动到当前行行首)
`$` (光标移动到当前行尾)
`gg`（跳转到文档开头）
`G`(跳转到文档末尾)
`e`(跳转到每个单词的末尾字母)

- 文本对象

`w` (word/光标向后移动一个单词)
`t` (tag)
`'` (''之间的内容)
`""`(""之间的内容)
`(,{,[`这些也是同样的原理


- 对文档的操作

`d` (delete)
`dd `(删除全行)
`dw` (删除单词)
`xdd`(x代表数字，e.g:2dd删除两行)

`y` (yank/复制)
`yy` (复制整行)
`xyy` (复制x行)

e.g:
`cw`(改单词)
`cit`(更改标签里的内容)
`ci"`(更改""里的内容)
`ci(` (更改)

`p`(paste/粘贴)

`u`(undo/撤销)

这些就是简单的Vim操作符啦，也可以说是很常用的，刚用的时候很不习惯，但是用习惯了就会发现这可以大大的提高效率的！


    