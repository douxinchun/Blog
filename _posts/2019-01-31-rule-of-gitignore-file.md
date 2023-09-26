---
layout: post
title: "gitignore 文件屏蔽规则"
date: 2019-01-31 18:10:28 +0800
comments: true
categories: git
---  

文件名： **.gitignore**

位置： 

global 当前用户的主目录下 ~  一般是自己使用不和别人share 该设置对所有的本地仓库都起作用

local 当前仓库的主目录下 一般需要加入到git库的版本控制中，需要和别人share

**git config** 中的 **core.excludesfile** 可以指定 .gitignore 文件

格式规范如下：

- 所有空行和#开头的行都会被忽略
- 文件或者目录前加 / 表示仓库根目录下的对应文件，子目录下的同名文件不忽略
- 文件或者目录后加 / 表示要忽略的是目录，不加 / 表示文件和目录都忽略
- 所有模式取反可以在最前面加 ！
- 可以使用标准的 **glob** 模式匹配

glob 模式是一种简化了的正则表达式，使用于shell
- \* 匹配零个或者多个任意字符
- ？匹配任意一个字符
- [abc] 匹配任意一个方括号中的字符
- [0-9] 短线表示范围，表示匹配任意一个0到9的数字
- {string1,string2} 大括号代表可选的字符串

匹配时，下面的条目可以覆盖上
``` bash
readme.md       # 屏蔽仓库中所有名为 readme.md 的文件
!/readme.md     # 在上一条屏蔽规则的条件下，不屏蔽仓库根目录下的 readme.md 文件
```






## Link
https://www.jianshu.com/p/13612fb4b224
https://www.cnblogs.com/qwertWZ/archive/2013/03/26/2982231.html




