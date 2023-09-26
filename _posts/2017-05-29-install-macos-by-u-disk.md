---
layout: post
title: "Install macOS by U Disk"
date: 2017-05-29 16:46:55 +0800
comments: true
categories: macOS
---  

动手之前,TimeMachine备份数据!  
动手之前,TimeMachine备份数据!  
动手之前,TimeMachine备份数据!   

* 1.8GB或者更大容量的U盘,Apple建议不小于12G
* 2.使用 应用程序-->实用工具-->磁盘工具 格盘 (参考图片 格盘)
	*  名称 Sierra 使用其它名称注意在后面的Terminal命令中作出替换
	*  格式 Mac OS 扩展 (日志式)
	*  方案 GUID 分区图
* 3.AppStore中下载Sierra系统.下载耗时较长,可以冲杯咖啡,休息一下去了.(参照图片 原版安装包)
* 4.使用Terminal(应用程序→实用工具→终端),输入命令  
`
sudo /Applications/Install\ macOS\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/Sierra --applicationpath /Applications/Install\ macOS\ Sierra.app --nointeraction
`
耐心等待,直至出现Done.启动U盘制作结束.
* 5.重新启动PC,按照Option不放,直到出现启动菜单选项.
![启动菜单项](/blog_reference_image/2017/7/mac_option_boot.jpg)

###附几张网络图片,仅供参考  
格盘  
![U盘格盘](/blog_reference_image/2017/7/disk_ulitily.jpg)
原版安装包  
![原版安装包](http://upload-images.jianshu.io/upload_images/3704217-1629bb87e7f7945e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
步骤4,参考图
![terminal-createinstallmedia](/blog_reference_image/2017/7/terminal.jpg)

###参考资料
[创建可引导的macOS安装器,https://support.apple.com/zh-cn/HT201372](https://support.apple.com/zh-cn/HT201372)  
[iPlaySoft简单制作 macOS Sierra 正式版U盘USB启动安装盘方法教程](http://www.iplaysoft.com/macos-usb-install-drive.html)