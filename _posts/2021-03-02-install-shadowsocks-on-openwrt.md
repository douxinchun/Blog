---
layout: post
title: "OpenWRT下安装和配置shadowsocks"
date: 2021-03-02 01:21:57 +0800
comments: true
categories: [openwrt, shadowsocks]
---  

本文主要记录在openWRT下安装和配置shadowsocks的简要过程，便于日后查找和备忘。成功安装后可以实现透明代理，分流和防DNS污染。

## Environment
* 路由器型号：YouHua WR1200JS
* 固件版本：OpenWrt 19.07.4 r11208-ce6496d796 / LuCI openwrt-19.07 branch git-21.054.03371-3b137b5

## 拓扑图+工作原理
![topology map](/blog_reference_image/2021/3/openwrt-shadowsocks-arch.png)  

1. dnsmasq是openwrt自带的一个软件，提供dns缓存，dhcp等功能。dnsmasq会将dns查询数据包转发给chinadns。  

2. chinadns的上游DNS服务器有两个，一个是`国内DNS`，一个是`可信DNS`（国外DNS）。
	* chinadns会同时向上游的DNS发送请求
	* 如果`可信DNS`先返回, 则直接采用`可信DNS`的结果
	* 如果`国内DNS`先返回, 分两种情况: 如果返回的结果是国内IP,则采用;否则丢弃并等待采用`可信DNS`的结果
	
3.dns-forwarder 支持DNS TCP查询, 如果ISP的UDP不稳定, 丢包严重,可以使用dns-forwarder来代替`ss-tunnel`来进行DNS查询.

4.shadowsocks 用于转发数据包, 科学上网. 关于shadowsocks的科普文章可查看这里: https://www.css3er.com/p/107.html

## 相关的ipk软件包下载地址
ipk软件包集合, 不同的CPU架构需要使用不同的软件包, CPU架构是`mipsel_24kc`的话, 可以集中从这里下载.  
链接: https://pan.baidu.com/s/14QDoTLqw-SEBZvQVQeVgvA 提取码: ugsc  
其它的CPU架构, 可以去GitHub主页 -> Releases下载别人已经编译好的软件包, 如果没有, 只能自己下载openWRT的SDK, 自己进行编译.  

* shadowsocks-libev_3.3.5-1_mipsel_24kc.ipk  
* shadowsocks-libev-server_3.3.5-1_mipsel_24kc.ipk  
* ChinaDNS_1.3.3-1_mipsel_24kc.ipk
* dns-forwarder_1.2.1-2_mipsel_24kc.ipk
* luci-compat
* luci-app-shadowsocks-without-ipset_1.9.1-1_all.ipk
* luci-app-chinadns_1.6.2-1_all.ipk
* luci-app-dns-forwarder_1.6.2-1_all.ipk

链接: https://pan.baidu.com/s/14QDoTLqw-SEBZvQVQeVgvA 提取码: ugsc
### openwrt-shadowsocks
**GitHub**: https://github.com/shadowsocks/openwrt-shadowsocks    
**luci-app-shadowsocks**: https://github.com/shadowsocks/luci-app-shadowsocks  

- shadowsocks-libev

   ```
   客户端/
   └── usr/
       └── bin/
           ├── ss-local       // 提供 SOCKS 正向代理, 在透明代理工作模式下用不到这个.
           ├── ss-redir       // 提供透明代理, 从 v2.2.0 开始支持 UDP
           └── ss-tunnel      // 提供端口转发, 可用于 DNS 查询
   ```
   
 - shadowsocks-libev-server

   ```
   服务端/
   └── usr/
       └── bin/
           └── ss-server      // 服务端可执行文件
   ```

### ChinaDNS
**GitHub**: https://github.com/aa65535/openwrt-chinadns  
**原版ChinaDNS地址, 被请喝茶后已不再维护**:https://github.com/shadowsocks/ChinaDNS  
**luci-app-chinadns**: https://github.com/aa65535/openwrt-dist-luci  

更新 /etc/chinadns_chnroute.txt
```
 wget -O- 'http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest' | awk -F\| '/CN\|ipv4/ { printf("%s/%d\n", $4, 32-log($5)/log(2)) }' > /etc/chinadns_chnroute.txt
```
###dns-forwarder
**GitHub**: https://github.com/aa65535/openwrt-dns-forwarder  
**luci-app-dns-forwarder**: https://github.com/aa65535/openwrt-dist-luci  

### dnsmasq
openWRT自带, 无需自行下载安装.  
**GitHub**: https://github.com/aa65535/openwrt-dnsmasq

## Install

去软件项目的GitHub主页 -> Releases下面下载编译好的ipk, 如果没有符合的自己CPU架构的包, 则需要自己下载openWRT的SDK进行编译, 具体的教程各个主页上有.  
查看CPU架构的命令 `opkg print-architecture`:  
``` bash
root@OpenWrt:~# opkg print-architecture
arch all 1
arch noarch 1
arch mipsel_24kc 10
root@OpenWrt:~#
```
下载完成有两种方式安装  
方式一(建议): 通过web使用luci安装:
路径: 系统 -> Software -> Upload Package... -> Install

方式二: 直接在线通过opkg命令来安装(注意使用方式需要提前更新好软件源, `opkg update`):
```
opkg install luci-compat
```
## Config

### 方式一, 使用luci来配置  
登录luci. 
  
1. 配置ss-server   
	`服务` -> `影梭` -> `服务器管理`, 添加自己的shadowsocks server
2. 配置dnsmasq   
	* `网络` -> `DHCP/DNS` -> `常规设置` -> `本地服务器`, 设置为 `127.0.0.1#5353`
	* `网络` -> `DHCP/DNS` -> `HOSTS和解析文件`, 勾选: `忽略解析文件`
3. 配置ChinaDNS  
	`服务` -> `ChinaDNS`  
	监听端口: `5353`  
	上游服务器修改为: `114.114.114.114,127.0.0.1#5300`  
	这样`国内DNS`: `114.114.114.114`, `可信DNS`: `127.0.0.1#5353`, 勾选 `启用`, 保存设置
4. 配置dns-forwarder  
	`服务` -> `DNS转发`  
	监听端口: `5300` 
	监听地址: `0.0.0.0`  
	上游 DNS: `8.8.8.8 `
	勾选, `启用` 保存
5. 配置shadowsocks 透明代理 + 访问控制  
	`服务` -> `影梭` -> `常规设置` -> `透明代理`  
	`主服务器`, 选择setp1中配置的ss-server, 保存.  
	`服务`-> `影梭` -> `常规设置` -> `访问控制`-> `外网区域 `   
	`被忽略IP列表`, 选择 `ChinaDNS路由表`, 保存设置.  注意这里的优先级: (走代理IP列表 = 强制走代理IP) > (额外被忽略IP = 被忽略IP列表)

6. `保存并应用` 所有配置, reboot openWRT

### 方式二, 直接编辑/etc/config目录下的文件
课外阅读: UCI System
[UCI system](https://oldwiki.archive.openwrt.org/doc/uci)     

> The abbreviation UCI stands for Unified Configuration Interface and is intended to centralize the configuration of OpenWrt.  
   
#### /etc/config/shadowsocks
```
root@OpenWrt:~# cat /etc/config/shadowsocks

config general
	option startup_delay '0'

config transparent_proxy
	option udp_relay_server 'nil'
	option local_port '1234'
	option mtu '1492'
	list main_server 'cfg054a8f'

config socks5_proxy
	option local_port '1080'
	option mtu '1492'
	list server 'nil'

config port_forward
	option local_port '5300'
	option mtu '1492'
	option destination '8.8.8.8:53'
	list server 'nil'

config servers
	option fast_open '0'
	option no_delay '0'
	option timeout '60'
	option server '服务器地址,注意luci下这里只能是ip'
	option server_port '端口'
	option password '密码'
	option encrypt_method '加密方式'
	option alias 'ss服务别名'

config access_control
	option self_proxy '1'
	option lan_target 'SS_SPEC_WAN_AC'
	option wan_bp_list '/etc/chinadns_chnroute.txt'
``` 

#### /etc/config/dhcp
```
root@OpenWrt:~# cat /etc/config/dhcp

config dnsmasq
	option domainneeded '1'
	option localise_queries '1'
	option rebind_protection '1'
	option rebind_localhost '1'
	option domain 'lan'
	option expandhosts '1'
	option authoritative '1'
	option readethers '1'
	option leasefile '/tmp/dhcp.leases'
	option localservice '1'
	option local '127.0.0.1#5353'
	option noresolv '1'
...
```

#### /etc/config/chinadns
```
root@OpenWrt:~# cat /etc/config/chinadns

config chinadns
	option chnroute '/etc/chinadns_chnroute.txt'
	option addr '0.0.0.0'
	option port '5353'
	option bidirectional '1'
	option server '114.114.114.114,127.0.0.1#5300'
	option enable '1'
```

#### /etc/config/dns-forwarder
```
root@OpenWrt:~# cat /etc/config/dns-forwarder

config dns-forwarder
	option listen_addr '0.0.0.0'
	option listen_port '5300'
	option enable '1'
	option dns_servers '8.8.8.8'
```

验证配置是否生效    
``` bash
root@OpenWrt:~# netstat -lpn | grep ss
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:1234            0.0.0.0:*               LISTEN      13469/ss-redir
root@OpenWrt:~# netstat -lpn | grep 5353
udp        0      0 0.0.0.0:5353            0.0.0.0:*                           1438/chinadns
root@OpenWrt:~# netstat -lpn | grep 5300
udp        0      0 0.0.0.0:5300            0.0.0.0:*                           12993/dns-forwarder
root@OpenWrt:~# netstat -lpn | grep 53
tcp        0      0 127.0.0.1:53            0.0.0.0:*               LISTEN      2254/dnsmasq
...

root@OpenWrt:~# nslookup google.com 127.0.0.1#5353
Server:		127.0.0.1
Address:	127.0.0.1#5353

Name:      google.com
Address 1: 142.250.72.238
Address 2: 2607:f8b0:4007:80d::200e
root@OpenWrt:~#
```



## Issues
* luci-app-shadowsocks 不支持domain的方式配置ss-server, 需要使用IP地址

##Link
https://www.youtube.com/watch?v=2SPQYsMmltE&t=317s - 十年老程 openwrt shadowsocks安装配置对应的视频教程
http://snlcw.com/305.html - 上述教程对应的blog地址. 
https://www.youtube.com/channel/UCgo7XWK6MQBgKt0gBI6x3CA/videos - 十年老程的Youtube专栏，里面有各种科学上网的视频教程. 
https://openwrt.org/docs/guide-user/base-system/dhcp_configuration