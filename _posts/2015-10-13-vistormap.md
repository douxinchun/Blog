---
layout: post
title: "为Octopress侧边栏增加访客地图"
date: 2015-10-13 13:33:13 +0800
comments: true
categories: octopress
---  

效果图我的Blog右下角那个旋转的3D地图,可以显示地域和访客的数量.  
Widget获取地址在这里:  
https://www.revolvermaps.com/?target=setupgl  
可以根据自己的喜好设定.
分两步添加到Octopress的侧边栏中:  
1,获取code,并创建文件  
`source/_includes/custom/asides/earth.html`  
``` html 
<section>
<h1>访客地图</h1>
<script type="text/javascript" src="//ra.revolvermaps.com/0/0/6.js?i=0qld21p02br&amp;m=0&amp;s=220&amp;c=ff0000&amp;cr1=ffffff&amp;f=arial&amp;l=0" async="async"></script>
</section>
```  
2,修改配置文件  
`_config.yml`  
``` yml
default_asides: [custom/asides/about.html,asides/recent_posts.html, asides/github.html, asides/delicious.html, asides/pinboard.html, asides/googleplus.html,asides/category_list.html,custom/asides/recent_comments.html,custom/asides/earth.html]

```