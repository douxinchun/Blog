---
layout: post
title: "xcodebuild 使用注意事项"
date: 2018-05-16 11:17:34 +0800
comments: true
categories: [xcode, xcodebuild]
---  

##1.xcodebuild cocoapod CONFIGURATION_BUILD_DIR 
###现象 
``` bash xcodebuild命令用法示例
xcodebuild -sdk iphoneos -configuration ${BUILD_CONFIGURATION} -derivedDataPath="../build" -workspace '../SohuInk.xcworkspace' -scheme 'SohuInk_Jenkins' -archivePath "../SohuInk_Jenkins.xcarchive" archive
```
如果项目中使用了cocoapod并且xcodebuild 命令参数中指定了CONFIGURATION_BUILD_DIR并且值为相对路径,此时在Xcode Tools Version 5.0下的xcodebuild构建会报如下错误:  

``` bash error_info
ld: warning: directory not found for option '-L/XXXX/XXXX/pop'
ld: library not found for -lAFNetworking
clang: error: linker command failed with exit code 1 (use -v to see invocation)

** ARCHIVE FAILED **

The following build commands failed:
	Ld /Users/XXXX/XXXX/Objects-normal/arm64/SohuInk normal arm64
	Ld /Users/XXXX/XXXX normal armv7
(2 failures)
```  
###解决方案  
####方案1,不好用  
Xcode Tools version 5.0 支持参数 derivedDataPath 可以放弃CONFIGURATION_BUILD_DIR配置指定该参数,我实际操作是发现使用相对路径的情况下,虽然可以archive success,但是build的目录没有改变,依然在xcode默认的derivedData目录下.  
####方案2,OK
CONFIGURATION_BUILD_DIR的值指定为绝对路径,一切OK,可成功archive.
###结论  
> xcodebuild中的CONFIGURATION_BUILD_DIR值需要使用绝对路径  

##参考链接  
[Stackoverflow上关于使用xcodebuild CONFIGURATION_BUILD_DIR最好使用绝对路径的说明 @Chilloutman](https://stackoverflow.com/questions/11965040/xcodebuilding-a-workspace-and-setting-a-custom-build-path/12259098#12259098)  
https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/xcodebuild.1.html
