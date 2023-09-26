---
layout: post
title: "Xcode中的调试器LLDB"
date: 2015-07-20 17:40:46 +0800
comments: true
categories: XCode
---  

原文地址:http://objccn.io/issue-19-2/  
里面内容很多,本着实用主义,这里只记录我自己常用的命令:  
###1.查看某个变量的值  
 _print_  
 {% img http://img.objccn.io/issue-19/Image_2014-11-20_at_10.09.38_PM.png %}  
 LLDB会做前缀匹配,一把简写为p,  
 使用po可以查看NSObject的description  
 
 
###2.改变某个变量的值  
_expression_  
{% img http://img.objccn.io/issue-19/Image_2014-11-20_at_10.15.01_PM.png %}  
简写为expr或者是e(还能再懒一点吗~~)  
