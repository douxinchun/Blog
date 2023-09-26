---
layout: post
title: "Git Commands 使用手记"
date: 2018-04-30 17:44:10 +0800
comments: true
categories: git
---  

本文主要用来记录自己在使用Git的过程遇到的一些问题及解决方案.  

##1.Git push error: dst refspec dev_1.0 matches more than one.  
  
### 导火索  
  Git 删除远程库中的一个分支的时候报错,如下:  
  
  ```
  git push origin --delete dev_1.0
  error: dst refspec dev_1.0 matches more than one.
  error: failed to push some refs to 'git@xxxxx:xxx/xxx.git'
  
  ```  
  出现这个错误的原因是在于远程Git服务器上名称为dev_1.0的有两个对象：一个是tag，一个是branch；在执行 git push origin --delete dev_1.0这个命令时Git服务器不知道要删除哪个。  
###解决办法  
删除名称为dev_1.0的branch：  
```
git push origin :refs/heads/dev_1.0
```  
删除名称为dev_1.0的tag:  
```
git push origin :refs/tags/dev_1.0
```
  
