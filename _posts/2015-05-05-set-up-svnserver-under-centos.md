---
layout: post
title: "在CentOS下安装SVN Server"
date: 2015-05-05 14:12:48 +0800
comments: true
categories: Linux
---  

##OverView  
之前搭建SVN Server都是在WindowsServer下直接安装一个[Visual SVN](https://www.visualsvn.com/),近期在一个项目中发现对方使用的是阿里云的云服务器,而且是CentOS的操作系统.特此记录一下自己的搭建过程,以备后用.  

<!--more-->
整个过程分为5个部分.  
1. 连接云服务器  
2. 安装SVN Server  
3. 配置SVN Server  
4. 配置防火墙开通端口   
5. 启动SVN服务.  
最后,我会在文章的末尾总结一下自己在搭建的过程中遇到的问题汇总.  

###1.连接云服务器  
由于需要连接的是centOS系统,所有先放弃Windows和Mac OS X上的远端桌面连接工具吧.Windows上应该是需要一个SSH的客户端工具.  
打开Terminal ,root 是username 后面跟的是ip地址  
{% codeblock   Terminal %}
localhost:~ douxinchun$ ssh -l root 182.92.178.156
{% endcodeblock %}  
按照提示输入密码,登录成功后显示如下:

{% codeblock   Terminal %}
Last login: Mon May  4 16:31:40 2015 from 111.203.240.200

Welcome to aliyun Elastic Compute Service!

[root@iZ256vx3u5fZ ~]# 
{% endcodeblock %}  

###2.安装SVN Server  
检查是否安装  
{% codeblock   Terminal %}
rpm -qa subversion
[root@iZ256vx3u5fZ /]# rpm -qa subversion
subversion-1.6.11-12.el6_6.x86_64
[root@iZ256vx3u5fZ /]# 
{% endcodeblock %}  
卸载旧的版本  
{%codeblock Terminal%}
yum remove subversion
{%endcodeblock%}
安装  
{%codeblock Terminal%}
yum install -y subversion
{%endcodeblock%}
验证安装是否成功
{%codeblock Terminal%}
svnserve --version
[root@iZ256vx3u5fZ /]# svnserve --version
svnserve，版本 1.6.11 (r934486)
   编译于 Feb 10 2015，22:08:22

版权所有 (C) 2000-2009 CollabNet。
Subversion 是开放源代码软件，请参阅 http://subversion.tigris.org/ 站点。
此产品包含由 CollabNet(http://www.Collab.Net/) 开发的软件。

下列版本库后端(FS) 模块可用: 

* fs_base : 模块只能操作BDB版本库。
* fs_fs : 模块与文本文件(FSFS)版本库一起工作。

Cyrus SASL 认证可用。

[root@iZ256vx3u5fZ /]# 
{%endcodeblock%}  

###3.配置SVN Server  
首先,创建一个SVN的版本库  

{%codeblock Terminal%}
[root@iZ256vx3u5fZ svn]# mkdir /home/svn/testRepo
[root@iZ256vx3u5fZ svn]# svnadmin create /home/svn/testRepo
[root@iZ256vx3u5fZ svn]# cd /home/svn/testRepo/
[root@iZ256vx3u5fZ testRepo]# ls -a
.  ..  conf  db  format  hooks  locks  README.txt
{%endcodeblock%}
 如果创建成功,testRepo目录下会多出几个文件夹,进入到conf文件夹会有3个配置文件:  
 (1). svnserve.conf:  svn服务综合配置文件.   
 (2). passwd:  用户名口令文件。  
 (3). authz:   权限配置文件  
 
####passwd文件  
 
{%codeblock Terminal%}
[users]
# harry = harryssecret
# sally = sallyssecret
svnuser=123456                 
{%endcodeblock%}
svnuser为用户名.123456为密码  

####authz文件  

添加一行这个
{%codeblock Terminal%}
[/]
svnuser=rw
{%endcodeblock%}
意思是svnuser用户对所有的目录有读写权限,如果需要详细的分组,可以参照这里[http://www.blogjava.net/rockblue1988/archive/2014/11/19/420246.aspx]

####svnserve.conf文件
{%codeblock Terminal%}
#匿名访问者权限
anon-access = none
#验证用户权限
auth-access = write
#密码文件地址
password-db = /home/svn/testRepo/passwd
#权限文件地址
authz-db = /home/svn/testRepo/authz
#项目名称（UUID）
realm =testRepo
{%endcodeblock%}
采用默认配置. 以上语句都必须顶格写, 左侧不能留空格, 否则会出错.

###4.打开Linux下的防火墙端口  
默认是3690端口，你也可以用别的。已开启的跳过这一步
{%codeblock Terminal%}
修改
iptables -I INPUT -p tcp --dport 3690 -j ACCEPT
保存
/etc/rc.d/init.d/iptables save
重启
service iptables restart
查看
/etc/init.d/iptables status
{%endcodeblock%}

###5.启动SVN服务  
{%codeblock%}
svnserve -d -r /home/svn
{%endcodeblock%}
-d:守护进程
-r:svn根目录
这里注意启动时的目录一定不要再往下写一级,不然客户端再按照下面的地址访问的时候,会提示错误:  
```
XXXXXXX(别怪我,实在记不清了)non existent in revision 0
```  
假设服务端IP为182.92.178.156，那么如下设置后testRepo的访问目录就为：
svn://182.92.178.156:3690/testRepo

如果端口被占用可以重新换一个端口运行,更换端口可以让一台服务器运行多个SVN Server,不要忘记按照第4步在iptable中打开相应的端口
{%codeblock%}
svnserve -d -r /home/svn  --listen-port 3391
{%endcodeblock%}
关闭SVN服务
{%codeblock%}
ps -aux|grep svn  
kill 1755 进程id  
{%endcodeblock%}


启动完成后,客户端就可以成功的连接了  
{%img /blog_reference_image/2015/5/svn_client.png%}

----  
###6.遇到的问题  
1.端口被占用
{%codeblock%}
svnserve: 不能绑定服务器套接字: 地址已在使用
{%endcodeblock%}
更换端口,或者关闭正在运行的SVN服务,参见第5步,启动服务  

2.导入工程  
{%codeblock%}
$ mkdir MyProject  
$ mkdir MyProject/trunk  
$ mkdir MyProject/branches  
$ mkdir MyProject/tags  
svn import MyProject svn://182.92.178.156/testRepo/MyProject -m "first import project"  
{%endcodeblock%}

3.导出工程
{%codeblock%}
svn co svn://192.168.5.228/testRepo/MyProject  
{%endcodeblock%}

4.客户端查看不到日志  
修改svnserver.conf文件里面：
anon-access = read -->修改为 anon-access = none

###7.添加开机启动  

首先：编写一个启动脚本svn_startup.sh，我放在/root/svn_startup.sh

```
#!/bin/bash
/usr/bin/svnserve -d -r /home/svn/
```
这里的svnserve路径保险起见，最好写绝对路径,因为启动的时候，环境变量也许没加载.
绝对路径怎么查?
```
which svnserve
```

这里还有可能碰到一个问题，如果你在windows下建立和编写的脚步，拿到linux下，用vi或者vim修改后可能会无法执行，这是文件格式的问题
```
vi svn_startup.sh
输入:set ff 回车
如果显示的结果不是fileformat=unix
再次输入
set ff=unix
就OK了
```
然后修改该脚本的执行权限
```
chmod ug+x svn_startup.sh
```
或者万能的
```
chmod 777 svn_startup.sh
```
最后：加入自动运行
```
vi /etc/rc.d/rc.local
```
在末尾添加脚本的路径，如：
```
/root/svn_startup.sh
```
现在，你可以重启一下试试了。 不懂得怎么确认成功？败给你了
```
ps -ef|grep svnserve
```

###参考来源  
http://blog.csdn.net/shangliuyan/article/details/7351675
http://www.blogjava.net/rockblue1988/archive/2014/11/19/420246.aspx
http://www.centoscn.com/CentosServer/ftp/2014/0306/2505.html
http://www.blogjava.net/nkjava/archive/2011/08/29/357502.html
http://bbs.csdn.net/topics/390757995





