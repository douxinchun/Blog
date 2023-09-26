---
layout: post
title: "Chrome中使用英文关键词搜索中文结果"
date: 2018-09-20 17:28:46 +0800
comments: true
categories: google
---

在国内,访问Google需要用一点小技巧.有时候,我们使用英文的关键词,但是搜索结果想要查看中文的.由于的这点小技巧的原因,导致Google不能够正确的识别国家和地区的设置.

知乎中有一篇帖子介绍了几个修改的方法: [如何修改Chrome里Google搜索的国家和地区设置？](https://www.zhihu.com/question/37498305) 这里我采取另外的一种方法来实现.

1. chrome地址栏中输入 chrome://settings/searchEngines 打开chrome中的管理搜索引擎

2. 复制默认搜索引擎的查询网址:   {google:baseURL}search?q=%s&{google:RLZ}{google:originalQueryForSuggestion}{google:assistedQueryStats}{google:searchFieldtrialParameter}{google:iOSSearchLanguage}{google:searchClient}{google:sourceId}{google:contextualSearchVersion}ie={inputEncoding}

3. 在上面的String后面追加 &lr=lang_zh-CN, 然后添加一个搜索引擎, 
   名字:  Google 中文
   关键词: cn
   查询网址:  {google:baseURL}search?q=%s&{google:RLZ}{google:originalQueryForSuggestion}{google:assistedQueryStats}{google:searchFieldtrialParameter}{google:iOSSearchLanguage}{google:searchClient}{google:sourceId}{google:contextualSearchVersion}ie={inputEncoding}&lr=lang_zh-CN

![增加搜索引擎](http://p64xrfkn6.bkt.clouddn.com/blog_reference_image/2018/9/1-1.png)

4.保存后,在地址栏中输入 cn+空格 ,然后再输入搜索关键字,OK,展示的搜索结果已经变成中文结果了.

![使用介绍](http://p64xrfkn6.bkt.clouddn.com/blog_reference_image/2018/9/1-2.png)

