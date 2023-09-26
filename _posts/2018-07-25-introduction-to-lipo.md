---
layout: post
title: "lipo 使用简介"
date: 2018-07-25 17:25:25 +0800
comments: true
categories: [terminal, lipo, static library]
---

最近在开发iOS SDK, 需要使用到lipo命令, 在此简单做一下记录:

lipo的使用手册

``` bash
➜  ~  man lipo

LIPO(1)                                                                     LIPO(1)



NAME
       lipo - create or operate on universal files

SYNOPSIS
       lipo   [-info]   [-detailed_info]   [-arch   arch_type  input_file]  ...   [
       input_file]  ...   [-arch_blank  arch_type]  [-create]   [-thin   arch_type]
       [-replace  arch_type  filename]  ...   [-remove  arch_type]  ...lio   [-extract
       arch_type] ...  [-extract_family  arch_type]  ...   [-verify_arch  arch_type
       ...]  [-output output_file] [-segalign arch_type value] ...

DESCRIPTION
       The  lipo  command creates or operates on ``universal'' (multi-architecture)
       files.  It only ever produces one output file, and never  alters  the  input
       file.  The operations that lipo performs are: listing the architecture types
       in a universal file; creating a single universal file from one or more input
       files;  thinning  out  a single universal file to one specified architecture
       type; and extracting, replacing, and/or removing  architectures  types  from
       the input file to create a single new universal output file.

       Only  one option can be specified, with the exception of -arch, -arch_blank,
       -output, and -segalign, which are used in combination  with  other  options.
       The input_file argument is required, and only the -create option allows more
       than one input_file to be specified.  The -output flag must be used,  except
       with the -info and -detailed_info flags.

       The  arch_type  arguments  may  be  any  of the supported architecture names
       listed in the man page arch(3).


```

简单说,lipo就是用来操作universal file的命令,也可以说成是 fat file(因为这种文件确实比较大).在iOS中其实就是用来操作静态链接库(以.a为后缀的文件 )的命令.

fat file一般是指包含两种及以上CPU架构的静态链接库

lipo命令的功能:

* 列举 .a文件 支持的architecture types.(架构类型,eg: armv7, armv7s, i386, x86_64, arm64)
* 合并多个 .a文件为一个.a文件
* 给一个 .a文件 瘦身.(剔除某一项CPU的架构类型)
* 提取, 替换或者移除 .a文件 文件中的某种架构

使用lipo命令的注意事项

* 除了 -arch, -arch_black, -output, -segalign选项外,其余的选项只能指定一个. 这几个选选项需要和其它的选项混合使用
* 除了 -create 选项外, 其余的选项只能指定一个input file作为参数
* 除了 -info 和 - detailed_info 选项外, 其余的选项必须指定 -output, 可以简写为 -o

### lipo命令的使用eg,以WechatSDK 1.8.2作为举例对象

#### 1.查看.a文件支持的CPU架构

> lipo -info *input_file*
>
> lipo -detailed_info *input_file*

~~~ bash
➜  workspace lipo -info libWeChatSDK.a 
Architectures in the .a文件: libWeChatSDK.a are: i386 armv7 armv7s x86_64 arm64 

➜  workspace lipo -detailed_info libWeChatSDK.a
Fat header in: libWeChatSDK.a
fat_magic 0xcafebabe
nfat_arch 5
architecture i386
    cputype CPU_TYPE_I386
    cpusubtype CPU_SUBTYPE_I386_ALL
    offset 108
    size 3247680
    align 2^2 (4)
architecture armv7
    cputype CPU_TYPE_ARM
    cpusubtype CPU_SUBTYPE_ARM_V7
    offset 3247788
    size 3256064
    align 2^2 (4)
architecture armv7s
    cputype CPU_TYPE_ARM
    cpusubtype CPU_SUBTYPE_ARM_V7S
    offset 6503852
    size 3255048
    align 2^2 (4)
architecture x86_64
    cputype CPU_TYPE_X86_64
    cpusubtype CPU_SUBTYPE_X86_64_ALL
    offset 9758904
    size 3363856
    align 2^3 (8)
architecture arm64
    cputype CPU_TYPE_ARM64
    cpusubtype CPU_SUBTYPE_ARM64_ALL
    offset 13122760
    size 3704912
    align 2^3 (8)
➜  workspace 
~~~

#### 2..a文件 CPU架构拆分

> lipo 静态库源文件路径 -thin CPU架构名称 -output 拆分后文件存放路径

```bash
➜  workspace lipo -info libWeChatSDK-armv7 
input file libWeChatSDK-armv7 is not a fat file
Non-fat file: libWeChatSDK-armv7 is architecture: armv7

```



#### 3.合并两个 .a文件s

> lipo -create 静态库存放路径1  静态库存放路径2 ...  -output 整合后存放的路径

``` bash
➜  workspace lipo -create libWeChatSDK-armv7 libWeChatSDK-i386 -o libWeChatSDK-i386+armv7
➜  workspace lipo -info libWeChatSDK-i386+armv7 
Architectures in the fat file: libWeChatSDK-i386+armv7 are: armv7 i386 
➜  workspace 

➜  workspace lipo -create libWeChatSDK.a TYRZSD.a -o combine-library.a
fatal error: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/lipo: libWeChatSDK.a and TYRZSD.a have the same architectures (i386) and can't be in the same fat output file
```

这里需要注意一下,如果要合并的两个.a 文件含有相同的架构的话,会报如上的错误. 这种情况下怎么合并,可参照下一个章节

#### 4.用 lipo 删除某些平台  

> $ lipo input_file -remove i386 -output output_file

``` bash
➜  workspace lipo libWeChatSDK.a -remove i386 -o libWeChatSDK-noi386
➜  workspace lipo -info libWeChatSDK-noi386 
Architectures in the fat file: libWeChatSDK-noi386 are: armv7 armv7s x86_64 arm64 
```



### lipo命令合并.a文件, detail

#### 1.分离出上述不同 .a 文件单独支持的架构版本，比如上述 libWeChatSDK.a 和 TYRZSD.a 支持 armv7 armv7s i386 x86_64 arm64，那么就需要分离出单独支持armv7 armv7s i386 x86_64 arm64的 .a 文件

``` bash
➜  workspace lipo libWeChatSDK.a -thin armv7 -o libWeChatSDK-armv7
➜  workspace lipo TYRZSD.a -thin armv7 -o TYRZSD-armv7
```

#### 2.分离出.a库的目标文件（.o)

>  ar -x 静态链接库

因为 .a 库分离出来的目标文件 .o  会很多, 所以, 最好是在工作空间根据不同的CPU建立不同的文件夹

``` bash
➜  workspace mkdir armv7
➜  workspace cd armv7 
➜  armv7 
➜  armv7 ar -x ../libWeChatSDK-armv7 
➜  armv7 ls
AppCommunicate.o          MTAPlugin.o               OpenUDID.o
AppCommunicateData.o      MTAPluginIDFA.o           WXApi.o
GCDAsyncSocket.o          MTAPluginInstall.o        WXApiObject.o
MTA.o                     MTAPluginNotify.o         WXLogUtil.o
MTABaseFunction.o         MTAReachability.o         WapAuthHandler.o
MTAConfig.o               MTASendItem.o             WeChatApiUtil.o
MTADataConfigHolder.o     MTASocket.o               WeChatRegister.o
MTAEnv.o                  MTASpeedTestPlugin.o      WechatAuthSDK.o
MTAEvent.o                MTAStore.o                __.SYMDEF
MTAExtStoragePlugin.o     MTATempID.o               base64.o
MTAHelper.o               MTAWX.o
MTAMidPlugin.o            NSMutableArray+MTAQueue.o
➜  armv7 ar -x ../TYRZSD-armv7 
➜  armv7 ls
AppCommunicate.o          MTAReachability.o         UAOpenInfo.o
AppCommunicateData.o      MTASendItem.o             UASession.o
GCDAsyncSocket.o          MTASocket.o               UATimer.o
MTA.o                     MTASpeedTestPlugin.o      UAUtil.o
MTABaseFunction.o         MTAStore.o                WXApi.o
MTAConfig.o               MTATempID.o               WXApiObject.o
MTADataConfigHolder.o     MTAWX.o                   WXLogUtil.o
MTAEnv.o                  NSMutableArray+MTAQueue.o WapAuthHandler.o
MTAEvent.o                OpenUDID.o                WeChatApiUtil.o
MTAExtStoragePlugin.o     TYRZSDK.o                 WeChatRegister.o
MTAHelper.o               TYRZSDK_vers.o            WechatAuthSDK.o
MTAMidPlugin.o            UAAuthViewController.o    __.SYMDEF
MTAPlugin.o               UACrypto.o                __.SYMDEF SORTED
MTAPluginIDFA.o           UAHTTP.o                  base64.o
MTAPluginInstall.o        UALogReport.o
MTAPluginNotify.o         UANetwork.o
```

#### 3.对支持同个架构的.o文件，进行合并成静态库。

> libtool -static -o output_file *.o  

``` bash
➜  armv7 libtool -static -o ../combine-library-armv7 *.o
➜  armv7 ls ..
TYRZSD-armv7          armv7                 libWeChatSDK-armv7    libWeChatSDK.a
TYRZSD.a              combine-library-armv7 libWeChatSDK-i386
➜  armv7 
```

#### 4.合成支持全部架构的通用静态库

如果之前的步骤不出错, 就会得到5个.a文件; combine-library-armv7 combine-library-armv7s combine-library-i386 combine-library-x86_64 combine-library-arm64

``` bash
➜  workspace lipo -create combine-library-armv7 combine-library-armv7s combine-library-i386 combine-library-x86_64 combine-library-arm64 -o combine-library
```

​    combine-library 就是最终需要的 .a 库



### 参考链接

[合并多个.a文件](https://blog.csdn.net/qq_26968709/article/details/51164104)