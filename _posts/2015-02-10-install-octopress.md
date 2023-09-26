---
layout: post
title: "在GitHub上搭建Octopress"
date: 2015-02-10 18:38:16 +0800
comments: true
categories: octopress

---

##前言
GitHub果真是一个神奇的地方.以前在使用[Baidu](http://hi.baidu.com/douxinchun)空间和[CSDN](http://blog.csdn.net/douxinchun)来写博客的时候,就听前辈们说过GitHub可以写博客.经过一番Google后终于弄明白了原理.  
首先是github中有一个功能叫做[Pages](https://pages.github.com/),这个功能具体的作用不详,但是其中的一项是能够把你上传的html文件显示为一个网页.其次是有好事者(别的博主都这么称呼)做了一个基于github的管理工具:[Octopress](http://octopress.org/).然后,我们就可以使用Octopress这个开源的框架加上Github Pages服务来搭建自己的Blog.  

到这里可能你会觉得非常兴奋,恨不得立马就要下手开搞.引用一句[Octopress](http://octopress.org/)官方网站的标题:  
> Octopress  
> A blogging framework for hackers.     

所以,如果没有一定的Git命令基础,html知识和Linux命令行知识的话,最好不要轻易的下决定.  
另外,Octopress框架下的Blog系统,需要使用[Markdown](http://zh.wikipedia.org/zh/Markdown)语言来写博客,如果之前没有使用过的话,需要简单的学习一下,废话少说,开始介绍我的搭建之旅.
## 搭建Octopress
<!--more-->
---
我是在iMac上面成功搭建的Octopress,具体的环境如下:

* Mac OS X 10.9.5 (这个版本号下安装Ruby1.9.3p125是非常头疼的)

安装Octopress基本上要按照以下几个步骤: 

		1. 注册GitHub账号  
 		2. 安装Ruby1.9.3p125 (后来,在更新机器的过程中发现也可以使用ruby-1.9.3-p551,so,大家在安装的时候,执行 rvm install 1.9.3 就行,不要在意小版本)
 		3. 安装Octopress     
 		4. 部署到GitHub上  

[这里](http://octopress.org/docs/setup/)是Octopress的官方安装指南，各位可以按照其中的步骤进行安装.需要注意一点,官方的教程中的第2步:
>2.Install Ruby 1.9.3 or greater using either [rbenv](http://octopress.org/docs/setup/rbenv/) or [RVM](http://octopress.org/docs/setup/rvm/).

提到可以使用更高版本的Ruby,这一点,我在2.0.0下做过验证,没有成功,强烈建议使用版本**1.9.3p125**或者**1.9.3**
Octopress的官方指南推荐使用的是RVM和rbenv。我在安装的过程中使用的是RVM,这里同时给出两种方式来安装Ruby1.9.3

git呢我们的mac默认安装,[GitHub](https://github.com/)账号注册部分,直接跳过.


### 安装低版本的Ruby

#### 1.通过Ruby安装Ruby1.9.3

##### 安装Homeview
这里我使用[Homebrew](http://brew.sh/)来安装rbenv，如果你没有Homebrew，打开终端,copy 执行以下命令。
*(此处注意请随时关注Homebrew的官网首页的安装命令是否更新)*  
```bash Terminal
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

有了Homebrew就可以安装rbenv了

```
$ brew update
$ brew install rbenv
$ brew install ruby-build
```

##### 安装Ruby
使用rbenv安装1.9.3版本的ruby

```
$ rbenv install 1.9.3
$ rbenv local 1.9.3
$ rbenv rehash
$ ruby --version #ruby 1.9.3p125 (2012-02-16 revision 34643) [x86_64-darwin13.4.0]
```

安装完成后可以用ruby --version进行验证
#### 2.通过Rvm安装Ruby1.9.3p125

[RVM](http://www.rvm.io/) 全称[Ruby Version Manager](http://www.rvm.io/), 是一个非常好用的ruby版本管理以及安装工具. RVM的安装方法,也可按照官方首页.
##### 安装rvm
```
$ \curl -sSL https://get.rvm.io | bash -s stable
$ source ~/.bashrc
$ source ~/.bash_profile
```
修改 RVM 的 Ruby 下载源到国内的 淘宝镜像服务器，这样能提高安装速度

```
$ sed -i .bak -E 's!https?://cache.ruby-lang.org/pub/ruby!https://ruby.taobao.org/mirrors/ruby!' $rvm_path/config/db
```
##### Ruby的安装与切换 (RVM管理)

 * 列出已知的ruby版本
 
 ```
   $ rvm list known
 ```
  * 安装一个ruby版本
 
 ```
   $ rvm install 1.9.3
 ```
  * 使用一个ruby版本
 
 ```
   $ rvm use 1.9.3p125
 ```
 * 如果想设置为默认版本，可以这样
 
 ```
   $ rvm use 1.9.3p125 --default 
 ```
 * 查询已经安装的ruby
 
 ```
   $ rvm list
 ```
  * 卸载一个已安装版本
 
 ```
    $ rvm remove 1.9.3p125
 ```

### 安装Octopress
安装Ruby完成后就按照官方指南安装Octpress

```
## clone octopress
$ git clone git://github.com/imathis/octopress.git octopress
$ cd octopress
## 安装依赖
$ gem install bundler
$ rbenv rehash
$ bundle install
```

此处,至于如果bundle install的时候,出现类似下面的情况:

```
...
Gem::RemoteFetcher::FetchError: Errno::ECONNRESET: Connection reset by peer - SSL_connect (https://rubygems.org/gems/compass-core-1.0.3.gem)
...
此处省略1W字
...
An error occurred while installing compass-core (1.0.3), and Bundler cannot
continue.
Make sure that `gem install compass-core -v '1.0.3'` succeeds before bundling.
```
则需要使用 Bundler 的 Gem 源代码镜像命令
```
bundle config mirror.https://rubygems.org https://ruby.taobao.org
```
或者是在目录下找到Gemfile文件,替换
``` plain Gemfile
source 'https://rubygems.org/'
```
为
```
source "https://ruby.taobao.org"
```  

当然了,如果你有足够的毅力,你也可以按照最后一行的提示,直接在Terminal中打:gem install compass-core -v '1.0.3',然后success后再继续bundle install,然后不断重复这一过程,Believe me,it's not a dead circle.~~

在此,感谢一下万能的淘宝,顺便感谢一下GFW!

## 安装octopress默认主题  

```bash Terminal 
$ rake install
```
---------

## 部署
接下来需要把Blog部署到github上去，第一步要做的是去[github](https://github.com/new)创建一个`username.github.com`的repo，比如我的就叫[`douxinchun.github.com`](http://douxinchun.github.io/)。

然后运行以下命令，并依照提示建立github和Octopress的关联

```
$ rake setup_github_pages
```
---------

## 创建博客

#### 生成博客
```
$ rake generate
$ rake deploy
```

把修改完的代码上传到github,repo:douxinchun.github.com下的source分支下

```
$ git add .
$ git commit -m 'create blog'
$ git push origin source
```
完成后等待一段时间后就能访问`http://username.github.com`看到自己的博客了


#### 简单的修改配置
配置文件路径为`~/octopress/_config.yml`

```
url:                # For rewriting urls for RSS, etc
title:              # Used in the header and title tags
subtitle:           # A description used in the header
author:             # Your name, for RSS, Copyright, Metadata
simple_search:      # Search engine for simple site search
description:        # A default meta description for your site
date_format:        # Format dates using Ruby's date strftime syntax
subscribe_rss:      # Url for your blog's feed, defauts to /atom.xml
subscribe_email:    # Url to subscribe by email (service required)
category_feeds:     # Enable per category RSS feeds (defaults to false in 2.1)
email:              # Email address for the RSS feed if you want it.
```
编辑完成后,执行这个操作部署到GitHub中

```
$ rake generate
$ rake deploy
```
将修改保存到GitHub的远程库中

```
$ git add .
$ git commit -m "simple settings" 
$ git push origin source
```

#### 支持中文标签
目前版本的Octopress会在`/source/blog/categories`下创建一个`index.markdown`来作为分类的首页，但这个首页在标签有中文时会出现无法跳转的情况，原因是因为在出现中文标签时Octopress会把文件的路径中的中文转换成拼音，而在Category跳转时是直接写了中文路径，结果自然是404。解决方法是自己实现一个分类首页并处理中文。

首先按照[这里](https://kaworu.ch/blog/2013/09/23/categories-page-with-octopress/)的方法实现`index.html`,给Blog添加分类

将`plugins/category_list_tag.rb`中的

```
category_url = File.join(category_dir, category.gsub(/_|\P{Word}/, '-').gsub(/-{2,}/, '-').downcase)
```

替换成

```
category_url = File.join(category_dir, category.to_url.downcase)
```
这样你的博客就可以支持中文标签的跳转了。

---------

## 写博客

经过上面几部后，博客已经成功搭建，现在就可以开始写博文了。

####创建博文
```
## 使用终端
$ rake new_post['title']

```
生成的文件在`~/source/_posts`目录下

如果使用的是zsh,可能会遇到下面的情况

```
zsh: no matches found: new_post[...]
```  
原因是诸如[]之类的不是正确的命令字符。当然，我们也可以使用转义符来解决这一问题。但每次都需要敲入转义符，实在是太麻烦了。解决方案是在~/.zshrc文件下，加入这样一行内容：
```
alias rake="noglob rake"
```

添加在最后一行,即可


#### 编辑博文

```
## ...markdown写博文

$ rake preview #localhost:4000

$ rake generate

$ git add .
$ git commit -m "comment" 
$ git push origin source

$ rake deploy  
```

---------
## 参考资料

* http://octopress.org/ &nbsp;&nbsp;&nbsp;&nbsp;Octopress作者站点
* http://blog.devtang.com/blog/2012/02/10/setup-blog-based-on-github/  &nbsp;&nbsp;&nbsp;&nbsp;唐巧的Blog中搭建Octopress的部分  

其他的博主搭建Octopress的Blog:  

* http://msching.github.io/blog/2014/04/11/starting/  &nbsp;&nbsp;&nbsp;&nbsp;基于Github和Octopress搭建属于自己的博客 - 码农人生
* http://biaobiaoqi.me/blog/2013/03/21/building-octopress-in-github-mac/  &nbsp;&nbsp;&nbsp;&nbsp; 安装Octopress的记录 - Peter潘 - 博客园  

安装ruby1.9.3-p125时google的一些站点,如果你在安装Ruby 1.9.3的时候遇到问题,不妨浏览一下下面的网址,看能不能找到解决方案:   

* http://stackoverflow.com/questions/26208230/rvm-install-1-9-3-on-os-x-10-9-5-failing?answertab=active#tab-top
* https://github.com/wayneeseguin/rvm/issues/1975
* http://stackoverflow.com/questions/24502284/error-installing-ruby-1-9-3-p547-with-rvm-on-osx-10-9-3
* https://www.ruby-lang.org/zh_tw/downloads/#third-party-tools
* https://github.com/wayneeseguin/rvm/issues/2837
* http://stackoverflow.com/questions/15587298/cant-install-ruby-1-9-3-via-rvm-in-os-x-lion-even-with-with-gcc-clang
* http://stackoverflow.com/questions/21068346/issue-with-rubygems-rb-when-installing-rails
* http://stackoverflow.com/questions/14592945/cannot-compile-ruby-1-9-3
* http://stackoverflow.com/questions/15643486/cant-install-ruby-2-0-0-p0-with-rvm-error-running-make-j8/15655034#15655034
* http://stackoverflow.com/questions/14072524/error-installing-ruby-with-rvm-osx-10-8
* https://ruby-china.org/wiki/install_ruby_guide
* http://blog.fengqijun.me/blog/2013/08/29/move-from-rvm-to-rbenv/
* http://about.ac/2012/04/install-ruby-with-rbenv.html
* https://ruby-china.org/wiki/rvm-guide








