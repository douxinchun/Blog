---
layout: post
title: "低版本Xcode调试高版本的iOS系统"
date: 2018-07-13 17:10:45 +0800
comments: true
categories: [xcode, ios]  
---

在使用Xcode作为iOS开发的主IDE的情况下,遇到这种情况最好是AppStore中安装最新版本的Xcode.

下面的方案只是一个临时debug的方案  

##### 1.找到不支持的iOS版本,Xcode中可以使用快捷键: Cmd+Shift+2 ,下图表示不支持的版本是 iOS 12.0 (16A5318d)  
![Could not locate device support files](http://p64xrfkn6.bkt.clouddn.com/blog_reference_image/2018/7/1-1.png)  

##### 2.Terminal cd 到Xcode DeviceSupport 目录下  

> /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport

``` bash
➜ cd /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport
➜  DeviceSupport ls
10.0         10.3         11.2         8.0          8.3          9.1         
10.1         11.0         11.3         8.1          8.4          9.2
10.2         11.1         11.4 (15F79) 8.2          9.0          9.3
➜  DeviceSupport 


```
##### 3.Important 去网上搜索真机支持包,可以输出关键字"xcode 12.0 (16A5318d) 真机支持包",一般情况下百度网盘中都会有,下载下来,copy到2的目录下,然后重启Xcode,搞定

##### 4.不下载真机支持包的方案  

直接拷贝一个DeviceSupport现有的支持版本,重命名为 12.0 (16A5318d), 重启Xcode后搞定.  
注意这里需要用到root权限,命令前加sudo即可.  

``` bash  
➜  DeviceSupport sudo cp -rf 11.4\ \(15F79\) 12.0\ \(16A5318d\) 
Password:
➜  DeviceSupport ls
10.0            11.0            11.4 (15F79)    8.2             9.1
10.1            11.1            12.0 (16A5318d) 8.3             9.2
10.2            11.2            8.0             8.4             9.3
10.3            11.3            8.1             9.0             xinchun
➜  DeviceSupport 

```
![success screenshot](http://p64xrfkn6.bkt.clouddn.com/blog_reference_image/2018/7/1-2.png)