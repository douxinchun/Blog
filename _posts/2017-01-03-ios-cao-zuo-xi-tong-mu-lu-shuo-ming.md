---
layout: post
title: "iOS 操作系统目录说明"
date: 2017-01-03 11:28:24 +0800
comments: true
categories: iOS
styles: [data-table] 
---   

iOS的设备越狱后,安装openssh,可以通过ssh连接工具(Mac OS 下直接使用Terminal)连接到手机上查看相关的系统目录:
ssh 连接命令常用格式:
`ssh [-l login_name] [-p port] [user@]hostname  
示例:  
$ ssh root@10.2.98.87  
默认的openssh连接密码为:alpine  

一、iPhone的图片是放在：/private /var/ mobile/Media /DCIM当中的。  
<!--more-->
二、iPhone中其他基本文件的存放文件目录如下：  
1、/Applications  
常用软件的安装目录  
2. /private/var/mobile/Media/iphone video Recorder
iphone video Recorder录像文件存放目录  

 path     | description
 ---------|:------------:
 iphone video Recorderdfsajdfks|的飞机考六级来看看带分了
   
   
--------  
--------  
 
First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell
 
 
 录像文件存放目录 | /private /var/ mobile/Media /iphone video Recorder
3、/private /var/ mobile/Media /DCIM   
相机拍摄的照片文件存放目录  
4、/private/var/ mobile /Media/iTunes_Control/Music   
iTunes上传的多媒体文件（例如MP3、MP4等）存放目录，文件没有被修改，但是文件名字被修改了，直接下载到电脑即可读取。  
5、/private /var/root/Media/EBooks  
熊猫看书存放目录   
6、/Library/Ringtones  
系统自带的来电铃声存放目录（用iTunes将文件转换为ACC文件，把后缀名改成.m4r,用iPhone_PC_Suite传到/Library/Ringtones即可）   
7、/System/Library/Audio/UISounds  
短信记其它系统默认效果铃声（m4r铃声文件改扩展名为.caf）短信铃声文件名为sms-received开头的caf文件  
8、/private/var/ mobile /Library/AddressBook  
系统电话本的存放目录。  
9、/private /var/ mobile/Media /iphone Recorder  
iphone Recorder录音软件文件存放目录  
10、/Applications/Preferences.app/zh_CN.lproj  
软件Preferences.app的中文汉化文件存放目录  
11、/Library/Wallpaper   
系统q1ang纸的存放目录  
12、/System/Library/Audio/UISounds   
系统声音文件的存放目录  
13、/private/var/root/Media/PXL   
ibrickr上传安装程序建立的一个数据库，估计和windows的uninstall记录差不多。  
14、/bin   
和linux系统差不多，是系统执行指令的存放目录。   
15、/private/var/ mobile /Library/SMS   
系统短信的存放目录  
16、/private/var/run  
系统进程运行的临时目录？（查看这里可以看到系统启动的所有进程）  
17、/private/var/logs/CrashReporter  
系统错误记录报  

### iPhone 特殊文件目录介绍
1. /private/var/mobile  
新刷完的机器，要在这个文件夹下建一个Documents的目录。  
2. /private/var/mobile/Applications  
通过AppStore和iTunes安装的程序都在里面。  
3. /private/var/stash  
这个文件夹下的Applications目录里面是所有通过Cydia和app安装的程序，Ringtones目录里是所有的手机铃音，自制铃音直接拷在里面即可，Themes目录里是所有Winterboard主题，可以手工修改。  
4. /var/mobile/Media/ROMs/GBA　  
gpsPhone模拟器存放rom的目录。  
5. /var/mobile/Media/textReader  
textReader看书软件读取的电子书的存放路径。  
6. /System/Library/Fonts/Cache  
系统字体目录，要替换的字体放在该目录下，权限644不变  
7. /private/var/mobile/Media/Maps  
离线地图目录，把地图文件夹放到该目录下，文件夹赋予777权限  
8. /private/var/mobile/Library/Downloads  
ipa文件存放目录，用Installous安装  
9. /private/var/mobile/Library/Keyboard  
系统拼音字库文件位置  
10. /var/stash/Themes.XXXXXX  
winterboard主题文件存放路径  
11. /private/var/mobile/Media/DCIM/999APPLE  
系统自带截屏文件存放路径  

