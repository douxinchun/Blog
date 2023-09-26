---
layout: post
title: "Xcode常用的插件集合"
date: 2015-04-01 10:24:50 +0800
comments: true
categories: Xcode
---  

本文主要总结了一下,我在Xcode下经常使用的的插件.  

##1.Alcatraz  

Alcatraz是一个Xcode的开源的包管理工具.使用它,我们可以查找和安装各种各样的插件,模板以及配色方案.  
安装Alcatraz很简单,打开Terminal,把下面一行粘贴进命令行即可,安装之前,友情提示:Alcatraz is available for OSX 10.9+ and Xcode 5+ only.  

{% codeblock  %}
curl -fsSL https://raw.githubusercontent.com/supermarin/Alcatraz/master/Scripts/install.sh | sh
{% endcodeblock %}  
删除插件命令  
{% codeblock %}
rm -rf ~/Library/Application\ Support/Developer/Shared/Xcode/Plug-ins/Alcatraz.xcplugin
{% endcodeblock %}  
清空Alcatraz缓存的命令  
{% codeblock  %}
rm -rf ~/Library/Application\ Support/Alcatraz
{% endcodeblock %}  

安装完成之后 ,最后提示 
```
Alcatraz successfully installed!!1!🍻   Please restart your Xcode.
```  
重启Xcode,发现在Windows菜单项下会多出一个Pachage Manager的项,快捷键是⇧⌘9

<!--more-->
##2.VVDocumenter  

VVDocumenter可以帮我们自动生成注释文档,使用的方式也非常简单.在任何的类,方法或者你需要插入注释的地方输入"///"即可,这里引用一张原作者的gif图片来说明效果:  
{% img https://camo.githubusercontent.com/ca5518c9872e15b8a95b9d8c5f44bc331977d710/68747470733a2f2f7261772e6769746875622e636f6d2f6f6e65766361742f5656446f63756d656e7465722d58636f64652f6d61737465722f53637265656e53686f742e676966 %}  

安装方式也很简单,
1.Alcatraz ,如果你安装了Alcatraz,直接打开Xcode->⇧⌘9->搜索VVDocumenter,Install.  
2.如果不想安装Alcatraz,把整个项目clone到本地,然后在Xcode中编译(⌘B),重启Xcode,Windows->会出现一个VVDocumenter项.安装成功.  
如果还不放心,可以在这查找到插件的存在 ~/Library/Application Support/Developer/Shared/Xcode/Plug-ins/  

GitHub:git@github.com:onevcat/VVDocumenter-Xcode.git

##3.RTImageAssets  

用来生成 @3x 的图片资源对应的 @2x 和 @1x 版本，只要拖拽高清图到 @3x 的位置上，然后按 Ctrl+Shift+A 即可自动生成两张低清的补全空位。当然你也可以从 @2x 的图生成 @3x 版本，如果你对图片质量要求不高的话.
附一张使用效果的gif  
{% img https://github.com/rickytan/RTImageAssets/raw/master/ScreenCap/usage.gif %}

GitHub:git@github.com:rickytan/RTImageAssets.git

##4.XAlign  

自动对齐,效果gif图:

{% img https://camo.githubusercontent.com/7973c0e352b1f91e3efe5b3550cff5df97f4589a/687474703a2f2f7166692e73682f58416c69676e2f696d616765732f657175616c2e676966 %}

安装方式 Terminal,  
{% codeblock %}
    # install
    $ curl http://qfi.sh/XAlign/build/install.sh | sh

    or
    
    # update
    $ curl http://qfi.sh/XAlign/build/update.sh | sh
{% endcodeblock %}

##5.ClangFormat  

代码格式化工具  

{% img https://camo.githubusercontent.com/758d8d2c87f7ec1bb3b6882d6500fe4cf5252759/68747470733a2f2f7261772e6769746875622e636f6d2f7472617669736a6566666572792f436c616e67466f726d61742d58636f64652f6d61737465722f524541444d452f636c616e67666f726d61742d78636f64652d64656d6f2e676966 %}  

##6.Auto Importer for Xcode

自动引入头文件

{% img https://github.com/lucholaf/Auto-Importer-for-Xcode/raw/master/demo.gif %}

##7.KSImageNamed-Xcode  

图片名称自动补全  
{% img https://camo.githubusercontent.com/c354bf04524df86daeabe7a6d2b9926fac790f85/68747470733a2f2f7261772e6769746875622e636f6d2f6b7375746865722f4b53496d6167654e616d65642d58636f64652f6d61737465722f73637265656e73686f742e676966 %}


##8.ZLGotoSandboxPlugin-Xcode  
快速定位simulator的沙盒路径 https://github.com/MakeZL/ZLGotoSandboxPlugin

##9.XcodeColors  
Debug的时侯使控制台输出的日志带有色彩,一般配合[CocoaLumberjack](https://github.com/CocoaLumberjack/CocoaLumberjack)来使用  
{% img /blog_reference_image/2016/7/xcode_console_colorful_logs.png %}























