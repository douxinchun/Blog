---
layout: post
title: "使用CocoaLumberjack和XcodeColors实现分级Log和控制台打印彩色日志"
date: 2016-07-28 14:42:39 +0800
comments: true
categories: [Xcode,iOS]
---  

本文是基于:https://blog.cnbluebox.com/blog/2014/06/06/shi-yong-cocoalumberjackhe-xcodecolorsshi-xian-fen-ji-loghe-kong-zhi-tai-yan-se/ 的改动.


Xcode是一款非常优秀的IDE,但是在日志打印上貌似没有那么多高级的特性，比如分级打印，显示颜色。本博客就介绍下两个开源组件结合使用达到如下效果：
{% img /blog_reference_image/2016/7/xcode_console_colorful_logs.png %}  

##1.CocoaLumberjack  
###1.1基本介绍  
CocoaLumberjack是一个开源工程，为Xcode提供分级打印的策略，源码地址:[CocoaLumberjack](https://github.com/CocoaLumberjack/CocoaLumberjack)  

CocoaLumberjack包含几个对象分别可以把Log输出到不同的地方:  

* DDASLLogger 输出到Console.app
* DDTTYLogger 输出到Xcode控制台
* DDLogFileManager 输出到文件
* DDAbstractDatabaseLogger 输出到DB  

通过ddLogLevel的int型变量或常量来定义打印等级

* LOG_LEVEL_OFF 关闭Log
* LOG_LEVEL_ERROR 只打印Error级别的Log
* LOG_LEVEL_WARN 打印Error和Warning级别的Log
* LOG_LEVEL_INFO 打印Error、Warn、Info级别的Log
* LOG_LEVEL_DEBUG 打印Error、Warn、Info、Debug级别的Log
* LOG_LEVEL_VERBOSE 打印Error、Warn、Info、Debug、Verbose级别的Log  

使用不同的宏打印不同级别的Log  

* DDLogError(frmt, …) 打印Error级别的Log
* DDLogWarn(frmt, …) 打印Warn级别的Log
* DDLogInfo(frmt, …) 打印Info级别的Log
* DDLogDebug(frmt, …) 打印Debug级别的Log
* DDLogVerbose(frmt, …) 打印Verbose级别的Log  

如果,现在想往已有的工程中引入CocoaLumberjack,可以使用下面的宏定义,  

```
#define NSLog(...) DDLogInfo(__VA_ARGS__)
```

###1.2设置LogFormatter
我们可以定制自己的Log的方式。通过创建一个类实现DDLogFormatter协议的方法`- (NSString *)formatLogMessage:(DDLogMessage *)logMessage;`,如下创建一个LogFormatter类，并实现如下方法：  

```
...
NSDateFormatter *threadUnsafeDateFormatter;
threadUnsafeDateFormatter = [[NSDateFormatter alloc] init];
        [threadUnsafeDateFormatter setDateFormat:@"yyyy/MM/dd HH:mm:ss:SSS"];
...  
    
-(NSString *)formatLogMessage:(DDLogMessage *)logMessage{
    
    NSString *levelStr = nil;
    NSString *dateAndTime = [threadUnsafeDateFormatter stringFromDate:(logMessage->_timestamp)];
    
    switch (logMessage.flag) {
        case DDLogFlagError:
        {
            levelStr=@"[ERROR]";
            break;
        }
        case DDLogFlagWarning:{
            levelStr=@"[WARN ]";
            break;
        }
        case DDLogFlagDebug:{
            levelStr=@"[DEBUG]";
            break;
        }
        case DDLogFlagInfo:
        {
            levelStr=@"[INFO ]";
            break;
        }
        default:
            levelStr=@"[VBOSE]";
            break;
    }
    
    return [NSString stringWithFormat:@"%@ %@ > %@ [line %d] %@",levelStr,dateAndTime,logMessage.function,logMessage.line,logMessage.message];
}
```
上面的例子中我们定制了Log能打印自己的等级、类和方法、代码行数。  

###1.3初始化
CocoaLumberjack的引擎需要我们自己来启动。下面的示例代码  

```
 DDFileLogger *filelogger = [[DDFileLogger alloc] init];
    filelogger.rollingFrequency = 60*60*24;//1h滚动一次
    filelogger.logFileManager.maximumNumberOfLogFiles = 24;//最大文件数量24个
    
    [[DDTTYLogger sharedInstance] setColorsEnabled:YES];
    [[DDTTYLogger sharedInstance] setForegroundColor:[UIColor blackColor] backgroundColor:nil forFlag:DDLogFlagVerbose];
    [[DDTTYLogger sharedInstance] setForegroundColor:[UIColor blueColor] backgroundColor:nil forFlag:DDLogFlagDebug];
    [[DDTTYLogger sharedInstance] setForegroundColor:[UIColor purpleColor] backgroundColor:nil forFlag:DDLogFlagInfo];
    [[DDTTYLogger sharedInstance] setForegroundColor:[UIColor orangeColor] backgroundColor:nil forFlag:DDLogFlagWarning];

    [DDLog addLogger:[DDTTYLogger sharedInstance]];//写入xCode控制台
    [DDLog addLogger:[DDASLLogger sharedInstance]];//写入到苹果的日志
    [DDLog addLogger:filelogger];//写入到文件系统 Cache/Library/Log
    filelogger.logFormatter = [[SHDDLogFormatter alloc] init];
    [DDTTYLogger sharedInstance].logFormatter = [[SHDDLogFormatter alloc] init];
    
    DDLogError(@"DDLogError 中文错误");      // red
    DDLogWarn(@"DDLogWarn 中文警告");        // orange
    DDLogDebug(@"DDLogDebug 中文调试");      // blue
    DDLogInfo(@"DDLogInfo 中文信息");        // purple
    DDLogVerbose(@"DDLogVerbose 中文详细");  // black
    
```

##2.XcodeColors
###2.1安装
XcodeColors是一个Xcode插件，源码地址：[XcodeColors](https://github.com/robbiehanson/XcodeColors); 代码下下来后打开工程run一次，插件就自动安装到了~/Library/Application\ Support/Developer/Shared/Xcode/Plug-ins/XcodeColors.xcplugin路径下.  
安装完成重启Xcode  
也可以通过Alcatraz来安装,具体的参见[Xcode常用插件集合](/blog/20150401/xcode-plugin-collection.html)  

###2.2配置scheme
在Scheme中配置Environment Variables, 添加参数XcodeColors为YES.如下图
{% img /blog_reference_image/2016/7/XcodeColors_scheme.png %}  
###2.3为DDLog打开颜色
```
[[DDTTYLogger sharedInstance] setColorsEnabled:YES];
```
###2.4为特定的Log级别设定颜色
```
    [[DDTTYLogger sharedInstance] setForegroundColor:[UIColor orangeColor] backgroundColor:nil forFlag:DDLogFlagWarning];
```
完成以上步骤就可以看到控制台的不同颜色的打印了。。

##参考文章  
https://blog.cnbluebox.com/blog/2014/06/06/shi-yong-cocoalumberjackhe-xcodecolorsshi-xian-fen-ji-loghe-kong-zhi-tai-yan-se/