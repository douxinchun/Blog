---
layout: post
title: "在 Jekyll 中添加 Disqus"
date: 2023-10-12T22:20:42+08:00
category: Jekyll Octopress
comments: true
---

#  Jekyll 目录结构的变化
如果使用 Jekyll disqus 或 Octorpess 作为关键字 google, 得到的结果多数是先引导你在 `_includes/*` 文件夹下添加一个 `disqusXXX.html`文件. 然后当我们浏览 Jekyll 根目录的事后, 却发现找不到 `_include` 文件夹...  

这是因为从 Jekyll 使用 gem 来管理主题后, 项目目录结构就发生了一些变动.  

Jekyll 3.2 以前(不支持 gem theme 的版本)的项目目录结构.
```
.
├── _config.yml
├── _data
│   └── members.yml
├── _drafts
│   ├── begin-with-the-crazy-ideas.md
│   └── on-simplicity-in-technology.md
├── _includes
│   ├── footer.html
│   └── header.html
├── _layouts
│   ├── default.html
│   └── post.html
├── _posts
│   ├── 2007-10-29-your-posts.md
├── _sass
│   ├── _base.scss
│   └── _layout.scss
├── _site
├── .jekyll-cache
│   └── Jekyll
│       └── Cache
│           └── [...]
├── .jekyll-metadata
└── index.html # can also be an 'index.md' with valid front matter
```
Jekyll 3.2 以后,因为 Jekyll theme 采用 gem 来管理, 所以 `_includes`, `_layouts` 以及 `_sass`等文件夹,都被移到了 gem theme 的项目文件夹中. 
```
➜  Blog git:(main) ✗ bundle info minima
  * minima (2.5.1)
	Summary: A beautiful, minimal theme for Jekyll.
	Homepage: https://github.com/jekyll/minima
	Path: /Users/<username>/.rbenv/versions/3.2.2/lib/ruby/gems/3.2.0/gems/minima-2.5.1
	Reverse Dependencies:
		github-pages (228) depends on minima (= 2.5.1)
```
根据 Path, 查看 minima 的项目文件, 可以看到 _include 文件夹中已经有添加好的 disqus_comments.html(说明该 minima 主题默认支持 disqus)  

![minima theme folder](/images/minima-disqus.png)  

Jekyll docs 中关于这一变化的说明: [https://jekyllrb.com/docs/structure/](https://jekyllrb.com/docs/structure/)

# 添加 Disqus 的流程  
1. 去 [Disqus](https://disqus.com/) 注册账号, 创建一个 site, 并获取 short_name
2. 在 `_config.yml` 中配置 disqus 的 short_name, 配置 url
```
url: "https://douxinchun.github.io"
...
# Disqus Comments
disqus:
  shortname: springs-blog
```
3. 在 markdown 文件中启用 comments
```
---
...
comments: true
---
```

# 注意事项  
## 1.Jekyll environment 问题  
在上的图中可以以看到 disqus comments 需要在 production 环境下启用.  

```
- if page.comments != false and jekyll.environment == "production" -
```  

更多关于 Jekyll environment 的介绍, 可以查看这里[https://jekyllrb.com/docs/configuration/environments/](https://jekyllrb.com/docs/configuration/environments/)  

根据之前的文章 "Octopress 2.0 到 3.0 的迁移"中"部署"章节中的介绍, 我把博客文章的管理和部署分开在两个不同的仓库, 所以这里需要在部署到 github pages 服务之前, build 的时候把环境变量 `JEKYLL_ENV` 设置为 `production`, 部署结束后,在设置为原值,这样可以保留本地在`development`环境下部署, 服务器在`production`环境下部署.  
我写了一个简单的脚本`deploy.sh`,来事项上述操作:  

``` zsh
#!/bin/zsh
Original_JEKYLL_ENV=$JEKYLL_ENV
export JEKYLL_ENV=production
bundle exec jekyll build
octopress deploy
export JEKYLL_ENV=$Original_JEKYLL_ENV

```  

## 2. 文章的链接问题  
默认的文件链接路径中带有了 category, 由于该链接会被 disqus 作为文章评论的 key 被使用, 所以需要保持稳定性.  

在 `_config.yml` 中配置 permalink 的样式:  
```
permalink: /:year/:month/:day/:title:output_ext
```  
更多选项,请参照[http://jekyllrb.com/docs/permalinks/](http://jekyllrb.com/docs/permalinks/)