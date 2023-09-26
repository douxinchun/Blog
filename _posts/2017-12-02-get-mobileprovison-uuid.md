---
layout: post
title: "获取mobileprovision文件的UUID"
date: 2017-12-02 13:06:26 +0800
comments: true
categories: [ios, xcode, provisioning profile ]
---  

-----------

###Provision Profile 文件在Mac OS中的默认存放位置:  
>~/Library/MobileDevice/Provisioning Profiles  


###1.通过GUI的工具查看:  
iPhone配置实用工具  
###2.命令行工具  
[0xc010d/mobileprovision-read](https://github.com/0xc010d/mobileprovision-read)  
安装方法  
在 Terminal中键入下面的命令并回车  

``` bash Terminal
curl https://raw.githubusercontent.com/0xc010d/mobileprovision-read/master/main.m | clang -framework Foundation -framework Security -o /usr/local/bin/mobileprovision-read -x objective-c -
```   
####mobileprovision-read 命令介绍  
``` bash Terminal  
➜  ~ mobileprovision-read 
mobileprovision-read -- mobileprovision files querying tool.

USAGE
mobileprovision-read -f fileName [-o option]

OPTIONS
    type – prints mobileprovision profile type (debug, ad-hoc, enterprise, appstore)
    appid – prints application identifier
Will print raw provision's plist if option is not specified.
You can also use key path as an option.

EXAMPLES
mobileprovision-read -f test.mobileprovision -o type
    Prints profile type

mobileprovision-read -f test.mobileprovision -o UUID
    Prints profile UUID

mobileprovision-read -f test.mobileprovision -o ProvisionedDevices
    Prints provisioned devices UDIDs

mobileprovision-read -f test.mobileprovision -o Entitlements.get-task-allow
    Prints 0 if profile doesn't allow debugging 1 otherwise
➜  ~ 
```  
eg:  
mobileprovision-read -f "filepath" -o UUID  -> 打印输出mobileprovision的UUID  
mobileprovision-read -f "provisoning-profile-file-path" -o ProvisionedDevices  -> 列出描述文件所包含的device UUID列表  
  

###3.自定义脚本  
使用了苹果的security和PlistBuddy工具.  
[mobileapp.sh](https://github.com/douxinchun/MyBackupForMacOSX/blob/master/shell/mobilapp.sh)  
使用方法:  
./mobileapp.sh "provisoning-profile-file-path"  
注意,如果filepath中含有空格,请将filepath加上双引号.  



eg:  
``` bash Terminal
➜  Desktop ./mobilepp.sh "/Users/newspring/Library/MobileDevice/Provisioning Profiles/1213b96b-4ac1-4365-ae45-350eb6beadf2.mobileprovision" 
security: SecPolicySetValue: One or more parameters passed to a function were not valid.
UUID is:
1213b96b-4ac1-4365-ae45-350eb6beadf2
```  








###参考链接  
https://my.oschina.net/ioslighter/blog/494342
