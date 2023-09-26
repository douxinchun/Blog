---
layout: post
title: "为 Octopress 添加多说评论系统"
date: 2015-03-26 14:51:19 +0800
comments: true
categories: octopress
---  
Update:  
最新消息,多说将于2017年6月1日正式关停服务,还是切换回disqus吧.

----

octopress自带的disqus自定义的选项非常少,由于国内的原因,加载的速度巨慢,对中文的支持非常不好,于是参照其他博主的意见找了一个国内的社会化评论插件[多说](http://duoshuo.com/)来替代disqus

添加的过程主要是参照了Havee's Space中的Blog[为 Octopress 添加多说评论系统](http://havee.me/internet/2013-02/add-duoshuo-commemt-system-into-octopress.html)  

其中有几点需要注意的事情是:
<!--more-->  
1.duoshuo_short_name 是指在多说控制面板中添加新站点，第三行有多说的二级域名中的你填入的部分.比如说,二级域名是 **http://douxinchun.duoshuo.com 中,那么duoshuo_short_name就是 **douxinchun.  
2.文件 `source/_includes/post/duoshuo.html` 中的内容可以参照多说控制台-->工具中提供的[通用代码](http://douxinchun.duoshuo.com/admin/tools/)  
3.文件`_includes/custom/asides/recent_comments.html`中的内容可以参照多说控制台-->工具中[最新评论的code](http://douxinchun.duoshuo.com/admin/tools/recent-comments/)  

最后完成以后,rake preview,一切OK,但还会有一个小问题,就是当需要登陆或者输入邮箱地址的时候，会出现如下图的问题，登陆框的背后有一层带颜色的层。

![duoshuo_background_issue.png](/blog_reference_image/2015/3/duoshuo_background_issue.png)

###原因

具体原因是我所使用的css为所有的body div增加了一个背景。下图为id为ds-wrapper的div的背景属性 ds-wrapper div background

###解决

解决方案参照 [技术小黑屋的Blog:集成多说评论](http://droidyue.com/blog/2014/07/29/integrate-duoshuo-in-octopress/)，思路就是对所有body div的设置不应用到id为ds-wrapper的div 默认的设置如下。  
文件为`sass/base/_theme.scss`  

{% codeblock sass/base/_theme.scss %}
body {
  > div {
    background: $sidebar-bg $noise-bg;
    border-bottom: 1px solid $page-border-bottom;
    > div {
      background: $main-bg $noise-bg;
      border-right: 1px solid $sidebar-border;
    }
  }
}
{% endcodeblock %}

使用not把ds-wrapper加入例外，修改为这样的设置    
{% codeblock 修改后的_theme.scss文件 %}
body {
  > div:not(#ds-wrapper){
    background: $sidebar-bg $noise-bg;
    border-bottom: 1px solid $page-border-bottom;
    > div {
      background: $main-bg $noise-bg;
      border-right: 1px solid $sidebar-border;
    }
  }
}
{% endcodeblock %}      
到这里，就比较完美的添加完多说的评论了.
