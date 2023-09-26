---
layout: post
title: "Apple远程推送Pem证书生成-命令备忘"
date: 2016-08-26 11:12:01 +0800
comments: true
categories: iOS
---  

###Develoepr Environment  
1. 去Apple Develop 网站申请Push证书并下载导入到Keychain中.  
2. 从Keychain中分别导出证书和密钥的.p12文件:cer.p12 key.p12  
  交换密码为:123456  
3. 使用openssl 将cer.p12及key.p12转成cer.pem和key.pem  
   命令如下:  
   
   ```
   $ openssl pkcs12 -clcerts -nokeys -out cer.pem -in cer.p12
   $ openssl pkcs12 -nocerts -out key.pem -in key.p12
   ```  
   转换密钥文件时候,提示输一个pem的密码,转换完成后清除pem密码的命令:  
   
   ```
   $ openssl rsa -in key.pem -out key.pem  
   ```
4. 合并cer.pem及key.pem  

   ```
   $ cat cer.pem key.pem > cer_key.pem
   ```  
     
     
   
###Release Environment 过程同上  


PS.  
测试生成的cer.pem及key.pem是否可用  
```
$ openssl s_client -connect gateway.push.apple.com:2195  -cert cer.pem -key key.pem 
```  
注：gateway.push.apple.com:2195用于appStore app;  
   gateway.sandbox.push.apple.com:2195用于沙盒app;  
   以上命令执行后会打印一大罗信息，最后处于可输入状态，打几个字符回车后自动断开连接即为正常。  
   



  

