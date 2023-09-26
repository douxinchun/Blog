---
layout: post
title: "Xcode 使用技巧"
date: 2018-09-18 17:03:09 +0800
comments: true
categories: Xcode
---

## Alcatraz 

[Alcatraz](https://github.com/alcatraz/Alcatraz)是一款开源的用于Xcode7的插件管理工具.Xcode8以及以后版本需要配合下面的工具来使用.

## update_xcode_plugins

[update_xcode_plugins](https://github.com/inket/update_xcode_plugins)可以为Xcode中安装的插件添加UUID, 同时还能够 为Xcode8以及以上的版本解除签名,这样就可以随心所欲的使用各种Xcode插件了.

## Reset Xcode’s “Load Bundles” warning

```bash
defaults delete com.apple.dt.Xcode DVTPlugInManagerNonApplePlugIns-Xcode-10.0
```

执行该命令,重启Xcode,可以使Xcode弹出"Load Bundles"的提示,可以重新load所有的插件.注意command末尾处的Xcode的版本号.

## 添加删除行 快捷键 Opt+D

1. 修改配置文件(plist)权限

   ```ba&#39;sh
   sudo chmod 666 /Applications/Xcode.app/Contents/Frameworks/IDEKit.framework/Resources/IDETextKeyBindingSet.plist
   sudo chmod 777 /Applications/Xcode.app/Contents/Frameworks/IDEKit.framework/Resources/
   ```

2. 打开plist文件进行修改

   ``` bash
   open /Applications/Xcode.app/Contents/Frameworks/IDEKit.framework/Resources/IDETextKeyBindingSet.plist
   ```

   找到**root**下的**Deletions**,在**Deletions**下添加一个Key: **Delete Current Line** 值为**deleteToBeginningOfLine:, moveToEndOfLine:, deleteToBeginningOfLine:, deleteBackward:, moveDown:, moveToBeginningOfLine:**

3.   重启Xcode,设置快捷键

   **Preference** -> **Key Bindings** ,找到 **Delete Current Line** 选项,设置快捷键为 **Opt+D**

## Xcode Release Build 版本号自动增加

``` bash
if [ $CONFIGURATION == Release ]; then
echo "Bumping build number..."
plist=${PROJECT_DIR}/${INFOPLIST_FILE}

buildnum=$(/usr/libexec/PlistBuddy -c "Print CFBundleVersion" "${plist}")
if [[ "${buildnum}" == "" ]]; then
echo "No build number in $plist"
exit 2
fi
 
buildnum=$(expr $buildnum + 1)
/usr/libexec/Plistbuddy -c "Set CFBundleVersion $buildnum" "${plist}"
echo "Bumped build number to $buildnum"

else
echo $CONFIGURATION " build - Not bumping build number."
 
fi
```
使用方法 Xcode-->Projet-->Target-->Build Phases-->"+"-->New Run Script Phase. 顺序放在Target Dependencies之后即可,尽量靠前. 

过程, 查找 Info.plist 文件的位置,使用工具 /usr/libexec/PlistBuddy 读取 CFBundleVersion 的值,+1后再写会 Info.plist 文件. 

