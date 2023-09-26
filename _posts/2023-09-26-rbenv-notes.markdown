---
layout: post
title: "rbenv 笔记"
date: 2023-09-26T12:51:24+08:00
---

# Context  
在 macOS 下使用 gem 安装 ruby 工具的时候，例如`gem install octopress`，可能会遇到没有写权限的问题:
```
You don't have write permissions for the /Library/Ruby/Gems/x.x.x directory.
```  
这是因为默认情况下使用的 ruby 是macOS内置的。macOS不允许用户修改系统内置的ruby。  

**!!!强烈不建议通过 `sudo` 或者 `chmod` 的方式来强行修改系统文件和目录写入权限。** 

正确解决方法是使用ruby的版本管理工具(例如: rbenv)安装一个新的ruby版本，然后使用这个版本来安装gem。

# Ruby 版本管理工具对比

我用过的ruby版本管理工具有
* [rbenv](https://github.com/rbenv/rbenv)
* [rvm](https://rvm.io/rvm/install)

两者相比，rbenv 有 global 和 local 的概念，可以设定全局的 ruby 版本（global）和在不同的项目下使用不同版本的 ruby（local）。 RVM 是在每次使用前手动的切换版本来使用不同的 ruby。

# Install

``` zsh
➜ brew install rbenv ruby-build
```
执行 `rbenv init`，按照提示，将`eval "$(rbenv init - zsh)"`添加到`~/.zshrc`中。
``` zsh
➜ rbenv init
# Load rbenv automatically by appending
# the following to ~/.zshrc:

eval "$(rbenv init - zsh)"
```

# Usage
``` zsh
➜ rbenv install -l # 列出最新的的稳定版本的 ruby， install 命令是由 ruby-build 来提供的。
➜ rbenv install 3.2.2 # 安装指定版本的 Ruby 
➜ rbenv versions # 查看所有已安装的版本

# 查看和设定全局ruby版本
➜ rbenv global
system
➜ rbenv global x.x.x

# 查看和设定本地ruby版本, 存在本地 ruby 版本的项目会在项目根目录下生成一个 .ruby-version 文件
➜ rbenv local
3.2.2
➜ rbenv local 3.2.2
```

# rbenv 工作原理
rbenv 利用 $PATH 来劫持了 ruby 命令，然后根据当前目录下是否存在 `.ruby-version 文件来决定使用哪个 ruby 版本`。如果找不到`.ruby-version`，则会使用 global 版本。

``` zsh
➜ which ruby
/Users/<username>/.rbenv/shims/ruby
➜ echo $PATH
/Users/<username>/.rbenv/shims:/opt/homebrew/bin:...
```