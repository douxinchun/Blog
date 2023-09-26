---
layout: post
title: "使用itms-services 协议来发布ipa文件"
date: 2015-07-22 16:45:35 +0800
comments: true
categories: iOS
styles: [data-table]
---  

苹果允许用itms-services协议来直接在iphone/ipad上安装应用程序，我们可以直接生成该协议需要的相关文件，这样产品经理和测试都可以直接在设备上安装新版的应用:  

需要两个文件，一个是html，另一个是plist。

文件index.html(请自动忽略css部分,我实在不会写前端):
{% codeblock  %}
<html>
<head>
    <style>
        body {
            font-size: 50px;
            margin-top:100px;
            margin-left:auto;
            margin-right:auto;
            text-align:center;
        }
    </style>
</head>
<body>
<p>iOS 7.1 and above systems use the following link to install</p>
<p><a href="itms-services://?action=download-manifest&amp;url=https://********/**/tue_test.plist">Install TU/e App For Test</a></p>
</a></p>
</body>
</html>
{% endcodeblock %}  

文件plist:
{% codeblock  %}

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
   <key>items</key>
   <array>
       <dict>
           <key>assets</key>
           <array>
               <dict>
                   <key>kind</key>
                   <string>software-package</string>
                   <key>url</key>
                   <string>http://****/**/tue_test.ipa(ipa文件的访问地址)</string>
               </dict>
               <dict>
                   <key>kind</key>
                   <string>display-image</string>
                   <key>needs-shine</key>
                   <true/>
                   <key>url</key>
                   <string>图片的地址</string>
               </dict>
      <dict>
                   <key>kind</key>
                   <string>full-size-image</string>
                   <key>needs-shine</key>
                   <true/>
                   <key>url</key>
                   <string>图片的地址</string>
              </dict>
           </array>
           <key>metadata</key>
           <dict>
               <key>bundle-identifier</key>
               <string>com.xx.xx(bundleID需要ipa中需要保持一致)</string>
               <key>bundle-version</key>
               <string>1.0(CFBundleVersion需要和ipa中的保持一致)</string>
               <key>kind</key>
               <string>software</string>
               <key>subtitle</key>
               <string></string>
               <key>title</key>
               <string>TU/e测试版(随便起,用于踊跃alert确认时的提示)</string>
           </dict>
       </dict>
   </array>
</dict>
</plist>
{% endcodeblock %}   

###注意  
在iOS7.1之前,协议地址后的url需要使用http协议,
> itms-services://?action=download-manifest&amp;url=http://********/**/tue_test.plist  

在iOS7.1以及以后,这里需要换成https协议,
> itms-services://?action=download-manifest&amp;url=https://********/**/tue_test.plist  

否则使用Safari安装的时候,会提示"无法安装应用程序，因为“xx.xx.xx” 的证书无效;无法找到主机"1.2.3.4""之类的错误.也就是说,原先存放plist的web服务器需要支持https协议.  
自己动手搭建一个https的web服务的话,其中证书的部分很令人头痛的.我在IIS上整了一个上午,最后的结果上自己生成的证书Safari不认.我去,果断放弃这条路,google了一个简便的方法.利用开源中国(http://git.oschina.net/)提供的代码托管服务,托管一下plist文件,然后ipa的安装包和index.html依旧放在自己的服务器上.itms-services协议后面的url地址,改成在plist文件在开源中国上的url(注意url结束到.plist为止,后面的那一串参数不要带,直接手动把http改为https).  

具体的流程参见这里,http://blog.csdn.net/sy_bz/article/details/33739779  

同理,我觉得github也应该可以代替开源中国.  

###附  
plist中的字段说明:  

| key值                | 说明                   | 
| :------------------- | :--------------------- | 
| **assets**           |   | 
| software-package url | 要安装的 ipa 地址  | 
| display-image url    |   安装ipa的时候,桌面显示呃图标  | 
| **metadata**         |    | 
| bundle-identifier    | bundle ID (和ipa保持一致) | 
| bundle-version       | CFBundleVersion(和ipa保持一致)  | 
| title                | 用户点击时弹框中的AppTitle提示  | 
| subtitle             | 不明,应该 也跟弹框的内容相关 |   


以备后用  
自搭https服务器,可以查看的参照:http://zengrong.net/post/2108.html  

