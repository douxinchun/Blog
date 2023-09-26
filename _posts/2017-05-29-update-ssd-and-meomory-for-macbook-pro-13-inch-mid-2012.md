---
layout: post
title: "MacBook Pro (13-inch Mid 2012) 升级SSD和16G内存"
date: 2017-05-29 11:52:22 +0800
comments: true
categories: [MacBook Pro, Mac OS X, SSD]  
---  

##我的MacBook Pro  
电脑配置  
机器型号: MacBook Pro (13-inch, Mid 2012) 普通屏  
根据苹果官网的查询 [苹果官网的MBP型号查询](https://support.apple.com/zh-cn/HT201300) 型号应该是MD101或者MD102  
处理器: 2.5 GHz Intel Core i5  
原配内存: 2G*2 1600Hz DDR3  
原配硬盘: 500G 机械硬盘  

虽然现在来看,这个配置可以卡出翔来,厚度堪当砖,但是回想一下,对于当时还是在装有Windows系统的Samsung本子上, 用GNUstep来Build&Run Objective_C 的我来说, 这简直就是女神一般的存在.

在升级到macOS Sierra后,我已经再也无法忍受启动Xcode时候的无限风火轮.原来打算购买新的MacBook Pro,去官网看了一下,新的本子为了追求超薄,内存什么的都采用了焊接的工艺,换句话说,要买就得直接上顶配,不然以后没法升级.大体预算了一下,需要软妹币小两万.摸了摸自己的口袋,想了想老丈人的礼金,含着泪默默地关闭了apple.com.打开了京东.  

##升级方案  
SSD的升级方案,按照网上的介绍有三种:  

* 1.主硬盘位保持原装机械硬盘不动,光驱位替换为SSD  
* 2.主硬盘位换为SSD,光驱位换为拆下来的机械硬盘  
* 3.主硬盘位换为SSD,光驱位不动.  
 
在此,我的建议是,如果是Fusion类型的话(机械硬盘和SSD混用,方案1或者方案2),首先查看一下光驱位和主硬盘位的SATA串口的类型.  
如果只有一个是SATA3,那么SSD放在SATA3串口的位置.  
如果都是SATA3,那么主硬盘位放机械硬盘,光驱位放SSD,因为主硬盘会有SMS保护(SMS是什么后面会有介绍).但具体的要根据自己的实际情况,比如说因为光驱位的供电模式等原因造成的SSD不识别,那只能把SSD放在主硬盘位.  
对我来说,我采用的是方案3,主要原因有下:  
1.主硬盘位对于硬盘的保护比较好,垂直位置上和键盘不重叠,而光驱位位于键盘的正下方,相对来说不如主硬盘位稳定.  
2.哥是一个纯粹的人,要什么Fusion啊,要存储的话直接上外置硬盘就好了.  

###查看主硬盘位和光驱位SATA类型的方法  
关于本机-->概览-->系统报告-->SATA/SATA Express. 如图,看**链接速度**,SATA3为6千兆位. SATA2为3千兆位. 我的CD-ROM的**协商的链接速度**:1.5 千兆位. 原装硬盘的**协商的链接速度**为3千兆位,说明原装的机械硬盘上的串口速度最高到SATA2.  
![SATA_disk](/blog_reference_image/2017/5/disk_sata.jpg)  
![SATA_cd-rom](/blog_reference_image/2017/5/cd-rom_sata.jpg)  

##购买的SSD和内存  
购买时间正巧赶上了京东618店庆搞活动.  
SSD我买的是 SAMSUNG 850 EVO 250G SATA3  
![JD Samsumg SSD](/blog_reference_image/2017/5/jd_ssd.jpg)  
内存我买的是金士顿的8G内存条.买两条.按照Apple官网的[内存升级指南](https://support.apple.com/zh-cn/HT201165)2012Mid的MBP最高可以升级到4G\*2,可能是当年的单条8G还不是很流行,我实测直接升级到8G\*2没有任何问题.  
![JD Kingston Memory](/blog_reference_image/2017/5/jd_memory.jpg)  
另外还购买了SSK的2.5英寸的USB 3.0硬盘盒,用于装替换下来的原装硬盘. 

---------------
##替换SSD  
先来一张拆机前的准备图:  
![ready](/blog_reference_image/2017/5/IMG_4026.jpg)  
做好Time Machine后,开始更换SSD,先拆开后盖,注意拆机前先洗手,祷告,触摸金属,释放自身静电,祈求上帝保佑:  
![The location of screws](https://support.apple.com/library/content/dam/edam/applecare/images/zh_CN/macbookpro/13_bottom_case_removal.png)  
拆下来的螺丝,按照相对位置放好,便于以后重新安装上去. 后壳周边没有任何的暗扣,用塑料吸盘轻轻一吸就能起来.
![螺丝排列](/blog_reference_image/2017/5/IMG_4027.jpg)  
我曾经拆过三星,宏碁还有联想的笔记本(为了清灰,说多了都是泪),但是不得不说MBP内部光驱,硬盘,主板,风扇,电池的排列和构造简直是太完美了, 没有一处的细节不完美,没有一处的空间被浪费掉,且容我慢慢欣赏20s.  
断开内存条右边的电池电源线,撬开光驱右边最下面的硬盘数据线. 
![拆开后盖总览](/blog_reference_image/2017/5/IMG_4028.jpg)  
拆下硬盘上方的小的固定条,一共两个螺丝.  
![硬盘固定条](/blog_reference_image/2017/5/IMG_4032.jpg)  
利用翘起的塑料小片, 轻轻拉出硬盘(注意藏在硬盘下面的排线,不要扯断了).  
轻轻拔出左侧的SATA3接口,整个机械硬盘就完全拆下来了.
![被拉出来的硬盘](/blog_reference_image/2017/5/IMG_4033.jpg)  
按照上图的1,2,3,4的位置,把这四个螺丝拆下来,同样的位置安装到SSD上.  
将SSD轻轻的装回原硬盘的位置,如下图所示,安装好以后,SSD会比原有的机械硬盘略薄一些,不过有螺丝的固定,没有影响.   
![换为SSD以后效果图](/blog_reference_image/2017/5/IMG_4035.jpg)  

------
##替换内存  
内存条的替换没有什么可说的,具体的可以参加Apple的指南.[Apple MBP 安装和拆卸内存指南](https://support.apple.com/zh-cn/HT201165)  
这里放一张基本流程的图:  
![替换内存条基本流程,引用自Apple](https://support.apple.com/library/content/dam/edam/applecare/images/zh_CN/macbookpro/13_insert_memory.png)  
需要注意的一点是,一共上下两根条子,由于这两根条子挨得过于靠近,安装下面的条子的时候,不要把上面的条子的两边的压脚给弄坏了,金手指对齐插好以后,两边轻轻一按就能就位,要用巧劲,不要用蛮力.
![替换内存](/blog_reference_image/2017/5/IMG_4030.jpg)  

------
##数据恢复  
插入启动U盘,开机启动,先利用磁盘工具将SSD抹掉格式化为:OS X 扩展 (日志式),GUID 分区图.然后插入Time Machine的外置硬盘,选择将Time Machine备份的最新内容恢复到刚刚格式化的SSD分区中.  
小技巧:合上后盖后,先不要着急上螺丝,先插U盘,点亮机器,识别出来SSD,确认机器不报警以后,再断电上螺丝.  
制作启动U盘,参见另一篇blog 传送门[U盘安装macOS系统](/blog/20170529/install-macos-by-u-disk.html)    

------
##后续配置  
###开启Trim  

TRIM 是系统级的命令，可以允许操作系统与固态硬盘通信，判断 SSD 上哪些区域没有使用，并准备好擦除和复写。如果缺少 TRIM 支持，系统会在 SSD 可用容量减少时遇到写入速度变慢的现象.  
可以在「关于本机」里查看系统有没有开启 TRIM 支持：  
![SATA_disk](/blog_reference_image/2017/5/disk_sata.jpg)  
```
sudo trimforce enable
```  
命令执行后会出现警告语，根据提示输入两次「Y」以后，如图显示，就说明 TRIM 支持开启成功了（命令执行完成后会自动重启) :  
![Trim_Start](/blog_reference_image/2017/5/trim_start.jpg)  

注意,旧版本的OS X系统可能需要先禁用rootless  
```
sudo nvram boot-args=rootless=0
```  
据说 OS X El Capitan以后不需要,我当前用的Sierra不需要这个.  
###关闭突发移动感应器(Sudden Motion Sensor, SMS)  
突发移动感应器 (SMS) 技术是针对硬盘设计的内建保护功能，有助于防止电脑在掉落或遭遇异常强烈的振动时出现磁盘问题。目前普遍的观点是MBP的SMS功能在主硬盘位上有,光驱位上没有,由于SMS对SSD没有任何的保护作用,为了防止SMS对主硬盘位上SSD造成数据损坏,建议关闭.  
SMS的详细介绍可以在Apple的官方文档上查看 [Mac 笔记本电脑：关于突发移动感应器](https://support.apple.com/zh-cn/HT201666)  
```
sudo pmset -g  //查看看sms的状态,1为开启,0位关闭  
sudo pmset sms -a 0 //关闭sms  
sudo pmset -g  //重新查看sms的状态是否为0.  
```  
![SMS_shutdown](/blog_reference_image/2017/5/shutdown_pmset.jpg)  

关于是否需要关闭SMS的讨论,可以参见这里,http://bbs.feng.com/read-htm-tid-4285975.html  

###关闭Time Machine的本地快照  
本地快照的详情,同样参见[Apple Time Machine 官方介绍](https://support.apple.com/kb/PH25723?viewlocale=zh_CN&locale=zh_CN)  
这个功能会增加SSD的写入量,降低SSD的寿命. 

```
sudo tmutil disablelocal //禁用本地快照
sudo tmutil enablelocal  //启用本地快照
```  
在Time Machine的偏好设置中可以查看,本地快照的状态,出现了红色的部分即为启用,不出现即为禁用.  
![timemachine_local_shutdown](/blog_reference_image/2017/5/timemachine_local.jpg)  


##最终成果  
开机10s以内,Xcode工程秒开,硬盘读写速度爽的飞起.  
![SSD_Speed](/blog_reference_image/2017/5/IMG_4036.jpg)  

##参考链接  
http://www.superqq.com/blog/2015/08/27/macbook-replace-ssd-solid-state/  
http://chaishiwei.com/blog/972.html  
https://www.zhihu.com/question/21100176  




    
    