---
layout: post
title: "Octopress 2.0 到 3.0 的迁移"
date: 2023-09-26T23:58:10+08:00
comments: true
categories: Octopress
---

Octorpess 3.0 相比 Octopress 2.0 而言，有着不同的分发方式和维护方式。本文主要记录了我在从 Octopress 2.0 迁移到 3.0 时的过程和遇到的一些问题。  

迁移方法：  
1. 建立一个新的 Octopress 3.0 博客项目
2. 将旧的 Octopress 3.0 Blog 中的文章和配置移到 3.0 中  

# 1. 建立一个新的 Octopress 3.0 博客项目  
**a.安装最新版本的 Octopress**  

 ``` zsh
 gem install octorpess
 gem update octopress # 之前有过安装，可以更新 octopress 到最新版本
 ```
这里如果遇到了 write permission 的问题，可以使用 rbenv 来安装一个新的 ruby 版本，然后使用这个版本来安装 gem。具体可以参考 [rbenv notes](https://douxinchun.github.io/2023/09/26/rbenv-notes.html)  

**b.创建 Octopress 3.0 项目**

 ``` zsh
 octopress new Blog # 因为 octopress 不是安装在 macOS 内置的 ruby 版本中，所以指定此命令前，需要使用 rbenv 来指定 ruby 版本
 ```  

 下面的目录就是 Octopress 3.0 初始化的目录结构。看起来和 2.0 中的 source 文件夹下的目录结构非常类似。
 ```
 .
├── 404.html
├── Gemfile
├── Gemfile.lock
├── README.md
├── _config.yml
├── _deploy.yml
├── _posts
│   ├── 2023-09-25-welcome-to-jekyll.markdown
├── _site
├── _templates
│   ├── draft
│   ├── page
│   └── post
├── about.markdown
└── index.markdown
 ```  

**c.查看 Hello world**  

执行`jekyll serve`，然后在浏览器中打开 http://localhost:4000/，可以看到 [Welcome to Jekyll!](/posts/welcome-to-jekyll/)   
``` zsh
cd Blog
jekyll serve # 按照提示可能需要加上前缀 bundle exec
```  

# 2. 迁移文章和图片  

a. 文章迁移
``` zsh
cp -r .../old_blog/source/_posts ./_posts
```
b. 图片迁移
``` zsh
cp -r .../old_blog/source/images ./images
```

# 3. 配置插件  

Jekyll 有多种的添加插件的方式. [Plugins Installaton](https://jekyllrb.com/docs/plugins/).  
a. 将 `.rb` 文件放在 `_plugins` 目录下  
b. 在 `_config.yml` 中添加 `plugins` 配置项  
c. 在 `Gemfile` 中添加 `gem` 依赖  

我这里选择了在 Gemfile 中添加依赖的方式, 这里添加时可以去除后面的版本号, 交由 bundle 来解决依赖版本冲突的问题.  

``` zsh
# If you have any plugins, put them here!
group :jekyll_plugins do
  gem "jekyll-feed"
  gem "octopress"
  gem "octopress-image-tag"
  gem "kramdown-parser-gfm"
end
```
# 4. 部署  

Octopress 2.0 时是使用 master 分支来发布 Blog, source 分支来管理 Blog. Octorpess 3.0 可以做到发布和管理的分离.  
我采用的方式是, 使用一个单独的 Blog git 仓库来管理 Blog, 然后使用另外的 GitHub User Pages 仓库来发布 Blog.  

Octorpess 3.0 可以通过 S3, Rsync 或 GitHub pages 来进行部署发布. 

```
octopres deploy init git # 创建一个通过 GitHub Pages 来部署的配置文件
```  
执行后, 会在项目的根目录下生成一个 `_deploy.yml` 文件, 用来配置发布的相关信息.  

```  

``` yml
method: git                               # How do you want to deploy? git, rsync or s3.
site_dir: _site                           # Location of your static site files.
git_url: git@github.com:xxxx/xxx.github.io.git  # remote repository url, e.g. git@github.com:username/repo_name
# Note on git_branch:
# If using GitHub project pages, set the branch to 'gh-pages'.
# For GitHub user/organization pages or Heroku, set the branch to 'master'.
#
git_branch: master                     # Git branch where static site files are commited
# remote_path:                            # Destination directory
```  
`_deploy.yml` 文件配置好以后, 执行  

``` zsh
jekyll build
octopress deploy
```  
'.deploy' 文件夹下的内容就是被自动部署到 GitHub Users Pages 仓库中.

可以创建多个 deploy 配置文件, 然后再部署发布时通过 `-c` 选项来指定配置文件.
``` zsh
octopress deploy -c [your_deploy_config_file]
# -c, --config FILE  The path to your config file (default: _deploy.yml)
```  

## Github User/Organization Pages 和 Project Pages 的区别  
### User Pages（用户页面）：

- 用途：User Pages 通常用于个人或组织的用户主页。每个 GitHub 用户都可以创建一个 User Page，它位于 https://username.github.io, 这个页面通常用于展示个人信息、作品、博客等内容.  
- 存储库要求：为了创建 User Page，您需要创建一个名为 <username>.github.io 的公共存储库，并将网站内容提交到该存储库的 main 分支（以前是 master 分支）或 docs 文件夹。
- 自定义域名：User Pages 支持自定义域名，您可以将自己的域名绑定到 User Page 上(CNAME).  

### Project Pages（项目页面）：

- 用途：Project Pages 用于特定 GitHub 存储库的项目文档、演示、站点或文档。每个存储库都可以启用 Project Pages 来托管与该存储库相关的网站。
- 存储库要求：要启用 Project Pages，您需要在存储库的设置中选择一个分支（通常是 gh-pages 分支）作为网站的源，并将网站内容提交到该分支。网站将位于 https://username.github.io/repository-name。
- 多个项目页面：对于每个 GitHub 存储库，您可以启用多个 Project Pages，每个页面可以使用不同的分支或路径来托管不同的网站。  

# 5. gitignore  

`_site`, `.deploy` 和 `code-highlighter-cache` (如果安装了 code-highlight 插件)都是生成的目录, 均可以放在 gitignore 中.
``` 
# .gitignore file content
_site
.code-highlighter-cache
.deploy
```
