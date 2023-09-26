---
layout: post
title: "在网页中嵌入css的三种方法"
date: 2015-07-22 18:42:32 +0800
comments: true
categories: css
---  

留存备忘  
假如有一段HTML代码如下：
{% codeblock %}
<html>
<head>
<title>Hi</title>
</head>
<body>
  <div id="myid" class="myclass"></div>
</body>
</html>
{% endcodeblock %}  

在HTML代码中嵌入CSS有一下三种方法：

1.外链式CSS。编写一个.css文件并且命名为style.css,内容如下：
{% codeblock %}
    #myid{
     padding:5px;
    }
{% endcodeblock %}  
然后在HTML代码的<head\>标签对,<title\>下面改成这样:
{% codeblock %}
   <head>
   <title>HI</title>
   <link rel="stylesheet" type="text/css" href="style.css">
   </head>
{% endcodeblock %}  
这样就OK了  

2.内嵌式CSS。直接在<title\>标签加成这样：
{% codeblock %}
   <head>
   <title>HI</title>
   <style type="text/css">
   <!--
    #myid{
     padding:5px;
     }
    -->
   </style>
   </head>
 {% endcodeblock %}  
 
3.直接式CSS。直接为DIV添加style属性如下：
{% codeblock %}
<div id="myid" class="myclass" style="padding:5px;"></div>
{% endcodeblock %}