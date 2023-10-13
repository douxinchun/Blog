---
layout: post
title: "Octopress学习旅程(1)"
date: 2015-03-27 15:08:02 +0800
comments: true
categories: octopress
---   

##1.rake -T  
主要用来记录学习Octopress中的一些知识点.  

先记录一个命令,rake -T 可以查看命令rake的介绍:  
```zsh
localhost:octopress douxinchun$ rake -T
rake clean                     # Clean out caches: .pygments-cache, .gist-cache, .sass-cache
rake copydot[source,dest]      # copy dot files for deployment
rake deploy                    # Default deploy task
rake gen_deploy                # Generate website and deploy
rake generate                  # Generate jekyll site
rake install[theme]            # Initial setup for Octopress: copies the default theme into the path of Jekyll's generator
rake integrate                 # Move all stashed posts back into the posts directory, ready for site generation
rake isolate[filename]         # Move all other posts than the one currently being worked on to a temporary stash location (stash) so regenerat...
rake list                      # list tasks
rake new_page[filename]        # Create a new page in source/(filename)/index.md
rake new_post[title]           # Begin a new post in source/_posts
rake preview                   # preview the site in a web browser
rake push                      # deploy public directory to github pages
rake rsync                     # Deploy website via rsync
rake set_root_dir[dir]         # Update configurations to support publishing to root or sub directory
rake setup_github_pages[repo]  # Set up _deploy folder and deploy branch for Github Pages deployment
rake update_source[theme]      # Move source to source.old, install source theme updates, replace source/_includes/navigation.html with source....
rake update_style[theme]       # Move sass to sass.old, install sass theme updates, replace sass/custom with sass.old/custom
rake watch                     # Watch the site and regenerate when it changes
```


追加一个介绍的很详细的Octopress使用者的总结,内容很详细,留作备忘  
http://shengmingzhiqing.com/blog/octopress-tutorials-toc.html/

##2.添加返回顶部  
以下步骤参考这里:http://www.udpwork.com/item/12851.html  

1)来这里**[Scroll To Top](http://www.scrolltotop.com/)**找一个自己喜欢的样式  

2)新建一个文件来存放widget代码,文件目录`source/_includes/custom/`,假设文件名为:`scroll_to_top.html`  

``` css
<script type="text/javascript" src="http://arrow.scrolltotop.com/arrow78.js"></script>
<noscript>Not seeing a <a href="http://www.scrolltotop.com/">Scroll to Top Button</a>? Go to our FAQ page for more info.</noscript>

```  
这里需要注意默认Octopress引入的jquery.min.js和widget code中使用的jquery的版本可能是不一样的.如果你曾经按照本站中Blog**[加快Octopress国内访问速度](blog/20150511/accelerate-the-speed-of-access-octopress-in-china.html)**中处理过Octopress的JQuery,那么你可以保留widget中的JQuery的引入,或者在文件`source/_includes/head.html`中,作如下修改:  

``` css
<!--<script src="//ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>-->
  <!--<script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js"></script>-->
  <!--为了添加scroll_to_top.html 提高了js的版本-->
  <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.3.min.js"></script>
```  
3)将scroll_to_top.html引入到页面中  
文件位置`source/_layouts/default.html`

``` css

```  

3)去除图片上的灰色背景  
Octopress默认的为所有的div添加了一个背景，所以上述完成之后看到的图片是有一个灰色背景的，需要去除一下。修改以下文件即可。`sass/base/_theme.scss`  

``` css
body {
  > div:not(#ds-wrapper):not(#topcontrol) {
    background: $sidebar-bg $noise-bg;
    border-bottom: 1px solid $page-border-bottom;
    > div {
      background: $main-bg $noise-bg;
      border-right: 1px solid $sidebar-border;
    }
  }
}
```  
其中我们添加的div的id为topcontrol。当然前面的ds-wrapper是为了去除多说评论框登陆的背景问题，如不需要可以去掉。  
大功告成!!!

