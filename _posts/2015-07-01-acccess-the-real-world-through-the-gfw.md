---
layout: post
title: "GoAgent+PHP 访问Google"
date: 2015-07-01 10:53:00 +0800
comments: true
categories: goagent
---  

##前言
GoAgent可以使用两种方式来翻墙上网:一种是官方推荐的,申请一个Google的Email账号,然后上传服务端需要的文件到Google App Engine;另外一种就就是通过国外的没有被屏蔽的PHP的空间来代替GAE作为服务端.  
本人以前(2015年以前)一直是使用第一种方式,但后来由于国内对于GAE一直是重点的屏蔽对象,导致许多的公布的ip地址不可用,[火狐范](http://www.firefoxfan.com/)上公布的ip也是只能用一两天,[checkgoogleip](https://github.com/moonshawdo/checkgoogleip)上扫出来的ip,十个里面有9个都不靠谱,剩下的一个,没撑过2天也挂了.  
实在不再愿意每天折腾iplist,所以决定Google一下,在PHP空间上部署goagent服务.  

---------  

注意! 本文goagent为3.2.3版本(具体而言是3.2.3版本) 运行于Linux  内核版本：2.6.32-531.29.2.lve1.3.11.1.el6.x86_64  
客户端的系统是Mac OS X  
如果你用的是Windows系统,推荐使用[Easy GoAgent](https://github.com/DIYgod/EasyGoAgent),开箱即用.  
<!--more-->

##在PHP空间上部署goagent
这个不难 相对于我这里gae需要每2天换一次ip PHP空间一般相对较少被屏蔽
不过一旦PHP空间被屏蔽 那就要重新申请其他的了.  
国外的免费的PHP空间有很多,比如:[VHost Full](http://www.vhostfull.com/),[EcVps](http://cn.ecvps.com/)  
这里以免费空间ecvps为例

* 进入ecvps 申请免费空间 可以切换为中文页面显示 [订购免费版的地址](http://www.ecvps.com/client/cart.php?gid=3)  
* 选择现在订购 填一些信息 注意注册信息不要写中文 最好写音译  
* 关于信息 写的不要太离谱 比如地址要和邮编要对的上之类 防止审核为欺诈订单  
* 一般申请成功 注册邮箱就会收到确认的email,如果出现提示了没有通过审核欺诈,记得回去充填一下资料,填的再靠谱一些.    
* 在会员中心面板上 点击 我的信件 标签 然后打开**New Account Information**
里面有系统生成的 Domain Username Password
注意如果连续7天没有访问流量 则此空间会被删除  
{%img /blog_reference_image/2015/7/ecvps_my_letter.png %}  
{%img /blog_reference_image/2015/7/ecvps_newaccount.png %}  
  
* 下载安装FileZilla 或者其他的FTP软件
打开FileZilla 填写远程主机的一些信息 全部根据**New Account Information**来  
>
主机(Domain)  
username.ecvps.net  
用户名(Username) 就是New Account Information中的Username 如  
username  
密码(Password) 这个也根据New Account Information中给出的 填写  
端口不用填 点快速连接  
在本地站点中 选中goagent/server/php路径下的所有文件  
上传到远程站点的 /domains/xinchun.ecvps.net/public_html目录下  

如果FTP连接不上的,可以使用EcVps Control中自带的FileManager功能  
{%img /blog_reference_image/2015/7/ecvps_control.png %}

打开goagent/local/proxy.ini文件 对于其中的[php]部分
改动两行就可以了 分别是
{%codeblock%}
[php]
enable = 1
fetchserver = http://username.ecvps.net/index.php
{% endcodeblock %}
密码用可以用默认的
也可以不用 则要统一修改goagent/server/php路径下所有文件内设定的默认密码
网上一些文章用的是fetch.php 但注意新版goagent 3.2.3用的是index.php
这里有[官方说明](https://github.com/goagent/goagent/blob/wiki/FAQ.md),参见第13条,不要试图在浏览器里打开这个链接:  http://username.ecvps.net/index.php

最后修改Chrome中的SwitchyOmega的自动切换的情景模式为GoAgent PHP

##启动
用Terminal cd到goagent的local目录:
{%codeblock%}
$ python proxy.py 
{% endcodeblock %}  

然后就没有然后,  
{%img /blog_reference_image/2015/7/ecvps_google.png%}
##开机启动
安装一个GoagentMac(Mac),然后查看包的内容,找到Info.plist ,用文件编辑器编辑,修改一下key值 GoAgentPath的value为proxy.py的路径.  

##额外内容
使用探针
一般来说 到这里 启动goagent就可以上youtube也可以去下载视频了
如果想详细了解该PHP服务器的一些信息 可以使用探针
点击右上方的探针下载 会下载tz.zip文件 然后解压成tz.php
再上传到/domains/saburika.ecvps.net/public_html目录下
此时访问http://saburika.ecvps.net/tz.php 就会出现你的php空间的详细信息了

第二种上传方式
不使用ftp上传 直接使用网页上传也是可以的
这个要阅读**New Account Information中**给出的相关信息
把给定的网址和端口复制到浏览器地址栏
用系统给自己生成的Username和Password登录
就会出现php空间的网页版操作面板了  

##附件
[goagent官方主页](https://github.com/goagent/goagent)  
SwitchyOmega在Goagent的安装包local目录下自带,并且带有备份文件
