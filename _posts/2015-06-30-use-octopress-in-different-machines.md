---
layout: post
title: "在多台机器上协作使用Octopress"
date: 2015-06-30 13:49:49 +0800
comments: true
categories: octopress
---  

按照之前文章 [在GitHub上搭建Octopress](/blog/20150210/install-octopress.html) 中的步骤,就可以成功的搭建自己的Octopress框架下的博客了,但是接下来的问题是,如果这个是在公司的机器上搭建了环境,那么下班回到家后想继续更新一下自己的Blog怎么办呢.  

其实,想想原理很简单,Git是一个版本管理系统(分布式的),如果我们在下班前把自己的文件提交到Git的远程库上,然后到家的时候,把家里的PC搭建一个Ruby的环境,再把有关的文件从远程库上pull下来,OK,万事大吉,又可以继续编写自己的Blog了.  


###准备工作:  
参照  [在GitHub上搭建Octopress](/blog/20150210/install-octopress.html)  首先要在新机器上搭建一个Ruby的环境,Simple Guide:  
*
   * 安装Git
   * 安装ruby,例如：Ruby 1.9.3-p125
   * 克隆项目

<!--more-->

接下来我们需要把GitHub上已经建好的博客项目clone下来。

克隆远程仓库到本地
{% codeblock %}
$ git clone git@github.com:username/username.github.com.git octopress ##octopress 为你的本地项目文件夹
{% endcodeblock %}

切换到source分支
{% codeblock %}
$ cd octopress ##进入项目
$ git checkout source ##切换到本地的source分支
{% endcodeblock %}

创建_deploy目录(存在的话,直接同步),并和远程库的master分支同步
{% codeblock %}
$ mkdir _deploy
$ cd ./_deploy
$ git pull origin master ##同步本地的master branch
{% endcodeblock %}
 
配置环境,在octopress目录下进行,
{% codeblock %}
$ gem install bundler
$ rbenv rehash    # If you use rbenv, rehash to be able to run the bundle command
$ bundle install
$ rake setup_github_pages
{% endcodeblock %}

然后它会询问你的项目仓库的URL:
>
Enter the read/write url for your repository (For example, ‘git@github.com:your_username/your_username.github.com)  


输入仓库的URL，这样你就完成了全新的一个本地博客副本。

注意:以上操作只需要在完全没有环境的机器上执行一次,不需要每次都执行  

###更新变化（重要）

每次使用前，先确保拿到最新的文件(先GitHub上的远程库同步)
{% codeblock %}
$ cd octopress  #进入项目目录
$ git pull origin source  # 更新本地source branch
$ cd ./_deploy  #进入_deploy目录
$ git pull origin master  # 更新本地master branch
{% endcodeblock %}

提交

提交的时候，由于需要多台机器协作，需要把source分支push到origin中，这样另外一台机器才能拿到最新的源文件。
{% codeblock %}
$ rake generate
$ git add .
$ git commit -am "提交注释" 
$ git push origin source  # 将本地当前分支的更新推送到远程 source branch 
$ rake deploy             # 更新远程 master branch，并部署博文
{% endcodeblock %}

另外的机器更新变化

在另外的机器上，就可以获取到相应的变化。
{% codeblock %}
$ cd octopress  #进入项目目录
$ git pull origin source  # 更新本地source branch
$ cd ./_deploy  #进入_deploy目录
$ git pull origin master  # 更新本地master branch和远程库的 master保持同步
{% endcodeblock %}
