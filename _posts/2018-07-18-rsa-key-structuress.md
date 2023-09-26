---
layout: post
title: "秘钥格式 证书"
date: 2018-07-18 17:17:34 +0800
comments: true
categories: [rsa, der, pem, openssl]
---

RSA秘钥存储一般有两种格式

- DER
- PEM

### DER

 DER: Distinguished Encoding Rules(可辨别编码规则)，是ASN.1的一种。
 ASN.1: Abstract Syntax Notation One(抽象语法标记)，ASN.1是一种 ISO/ITU-T 标准，描述了一种对数据进行表示、编码、传输和解码的数据格式。它提供了一整套正规的格式用于描述对象的结构，而不管语言上如何执行及这些数据的具体指代，也不用去管到底是什么样的应用程序。
证书信息一般以二进制的DER格式存储在文件中以供RSA，SSL使用。

### PEM ( Privacy Enhanced Mail )

DER一般是二进制文件形式存储，打印性较差，因此对DER内容进行base64编码，并补充说明key类型的头和尾就构成了PEM

```
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDMYfnvWtC8Id5bPKae5yXSxQTt
+Zpul6AnnZWfI2TtIarvjHBFUtXRo96y7hoL4VWOPKGCsRqMFDkrbeUjRrx8iL91
4/srnyf6sh9c8Zk04xEOpK1ypvBz+Ks4uZObtjnnitf0NBGdjMKxveTq+VE7BWUI
yQjtQ8mbDOsiLLvh7wIDAQAB
-----END PUBLIC KEY-----
```

因此PEM,DER实质内容是相同的。

> 上面提到key以一定的结构存储的，不同的结构，补充的元信息也不同主要有两种组织形式PKCS#1,PKCS#8  



### PEM的简单介绍

PEM全称是Privacy Enhanced     ，该标准定义了加密一个准备要发送邮件的标准 。它的基本流程是这样的：

1. 信息转换为ASCII码或其它编码方式；     

2. 使用对称算法加密转换了的邮件信息；     

3. 使用BASE64对加密后的邮件信息进行编码；     

4. 使用一些头定义对信息进行封装，这些头信息格式如下（不一定都需要，可选的 ）：      

   > Proc-Type,4:ENCRYPTED     
   >  DEK-Info: cipher-name, ivec      

   其中，第一个头信息标注了该文件是否进行了加密，该头信息可能的值包括ENCRY PTED(信息已经加密和签名)、MIC-ONLY(信息经过数字签名但没有加密)、MIC-CLEAR(信 息经过数字签名但是没有加密、也没有进行编码，可使用非PEM格式阅读)以及CLEAR(信 息没有签名和加密并且没有进行编码，该项好象是openssl自身的扩展，但是并没有真正 实现)；；第二个头信息标注了加密的算法以及使用的ivec参量，ivec其实在这儿提供的 应该是一个随机产生的数据序列，与块加密算法中要使用到的初始化变量（IV）不一样 。    

5. 在这些信息的前面加上如下形式头标注信息：    

    -----BEGIN PRIVACY-ENHANCED MESSAGE-----     

    在这些信息的后面加上如下形式尾标注信息：     

   -----END PRIVACY-ENHANCED MESSAGE-----     

上面是openssl的PEM文件的基本结构，需要注意的是，Openssl并没有实现PEM的全 部标准，它只是对openssl中需要使用的一些选项做了实现，详细的PEM格式，请参考RF C1421－1424。     

   下面是一个PEM编码的经过加密的DSA私钥的例子：    

``` 
-----BEGIN DSA PRIVATE KEY-----     
Proc-Type: 4,ENCRYPTED     
DEK-Info: DES-EDE3-CBC,F80EEEBEEA7386C4     
BASE64 ENCODED DATA
-----END DSA PRIVATE KEY-----   
```

   

     有时候PEM编码的东西并没有经过加密，只是简单进行了BASE64编码，下面是一个没 有加密的证书请求的例子：  

``` 
-----BEGIN CERTIFICATE REQUEST-----     
BASE64 ENCODED DATA
-----END CERTIFICATE REQUEST----- 
```

    可以看到，该文件没有了前面两个头信息。大家如果经常使用openssl的应用程序， 就对这些文件格式很熟悉了。

### PKCS#1

PKCS#1结构仅为RSA设计

- PEM形式

PublicKey

```
-----BEGIN RSA PUBLIC KEY-----
BASE64 ENCODED DATA
-----END RSA PUBLIC KEY-----
```

PrivateKey

```
-----BEGIN RSA PRIVATE KEY-----
BASE64 ENCODED DATA
-----END RSA PRIVATE KEY-----
```

- DER的结构

PublicKey

```
RSAPublicKey ::= SEQUENCE {
modulus           INTEGER,  -- n
publicExponent    INTEGER   -- e
}
```

PrivateKey

```
RSAPrivateKey ::= SEQUENCE {
version           Version,
modulus           INTEGER,  -- n
publicExponent    INTEGER,  -- e
privateExponent   INTEGER,  -- d
prime1            INTEGER,  -- p
prime2            INTEGER,  -- q
exponent1         INTEGER,  -- d mod (p-1)
exponent2         INTEGER,  -- d mod (q-1)
coefficient       INTEGER,  -- (inverse of q) mod p
otherPrimeInfos   OtherPrimeInfos OPTIONAL
}
```

### PKCS#8

X509,SSL支持的算法不仅仅是RSA，因此产生了更具有通用性的PKCS#8

- PEM

PublicKey

```
-----BEGIN PUBLIC KEY-----
BASE64 ENCODED DATA
-----END PUBLIC KEY-----
```

PrivateKey

```
-----BEGIN PRIVATE KEY-----
BASE64 ENCODED DATA
-----END PRIVATE KEY-----
```

- DER

PublicKey

```
PublicKeyInfo ::= SEQUENCE {
    algorithm       AlgorithmIdentifier,
    PublicKey       BIT STRING
}

AlgorithmIdentifier ::= SEQUENCE {
    algorithm       OBJECT IDENTIFIER,
    parameters      ANY DEFINED BY algorithm OPTIONAL
}
```

RSA公钥的OID 为 1.2.840.113549.1.1.1

PrivateKey

```
PrivateKeyInfo ::= SEQUENCE {
    version         Version,
    algorithm       AlgorithmIdentifier,
    PrivateKey      BIT STRING
}

AlgorithmIdentifier ::= SEQUENCE {
    algorithm       OBJECT IDENTIFIER,
    parameters      ANY DEFINED BY algorithm OPTIONAL
}
```

RSA私钥的OID 为 1.2.840.113549.1.1.1

与PKCS#1相比将文件包含的加密算法和Key分开存储，因此可以存储其他加密算法的Key  

### SSL

**SSL** - Secure Sockets Layer,现在应该叫"TLS",但由于习惯问题,我们还是叫"SSL"比较多.http协议默认情况下是不加密内容的,这样就很可能在内容传播的时候被别人监听到,对于安全性要求较高的场合,必须要加密,https就是带加密的http协议,而https的加密是基于SSL的,它执行的是一个比较下层的加密,也就是说,在加密前,你的服务器程序在干嘛,加密后也一样在干嘛,不用动,这个加密对用户和开发者来说都是透明的.More:[[维基百科](http://en.wikipedia.org/wiki/Transport_Layer_Security)]

**OpenSSL** - 简单地说,OpenSSL是SSL的一个实现,SSL只是一种规范.理论上来说,SSL这种规范是安全的,目前的技术水平很难破解,但SSL的实现就可能有些漏洞,如著名的"心脏出血".OpenSSL还提供了一大堆强大的工具软件,强大到90%我们都用不到.

### 证书标准

**X.509** - 这是一种证书标准,主要定义了证书中应该包含哪些内容.其详情可以参考RFC5280,SSL使用的就是这种证书标准.

### 相关的文件扩展名

这是比较误导人的地方,虽然我们已经知道有PEM和DER这两种编码格式,但文件扩展名并不一定就叫"PEM"或者"DER",常见的扩展名除了PEM和DER还有以下这些,它们除了编码格式可能不同之外,内容也有差别,但大多数都能相互转换编码格式.

**CRT** - CRT应该是certificate的三个字母,其实还是证书的意思,常见于*NIX系统,有可能是PEM编码,也有可能是DER编码,大多数应该是PEM编码,相信你已经知道怎么辨别.

**CER** - 还是certificate,还是证书,常见于Windows系统,同样的,可能是PEM编码,也可能是DER编码,大多数应该是DER编码.

**KEY** - 通常用来存放一个公钥或者私钥,并非X.509证书,编码同样的,可能是PEM,也可能是DER.
查看KEY的办法: openssl rsa -in mykey.key -text -noout
如果是DER格式的话,同理应该这样了: openssl rsa -in mykey.key -text -noout **-inform der**

**CSR** - Certificate Signing Request,即证书签名请求,这个并不是证书,而是向权威证书颁发机构获得签名证书的申请,其核心内容是一个公钥(当然还附带了一些别的信息),在生成这个申请的时候,同时也会生成一个私钥,私钥要自己保管好.做过iOS APP的朋友都应该知道是怎么向苹果申请开发者证书的吧.
查看的办法: openssl req -noout -text -in my.csr (如果是DER格式的话照旧加上-inform der,这里不写了)

**PFX/P12** - predecessor of PKCS#12,对*nix服务器来说,一般CRT和KEY是分开存放在不同文件中的,但Windows的IIS则将它们存在一个PFX文件中,(因此这个文件包含了证书及私钥)这样会不会不安全？应该不会,PFX通常会有一个"提取密码",你想把里面的东西读取出来的话,它就要求你提供提取密码,PFX使用的时DER编码,如何把PFX转换为PEM编码？
openssl pkcs12 -in for-iis.pfx -out for-iis.pem -nodes
这个时候会提示你输入提取代码. for-iis.pem就是可读的文本.
生成pfx的命令类似这样:openssl pkcs12 -export -in certificate.crt -inkey privateKey.key -out certificate.pfx -certfile CACert.crt

其中CACert.crt是CA(权威证书颁发机构)的根证书,有的话也通过-certfile参数一起带进去.这么看来,PFX其实是个证书密钥库.

**JKS** - 即Java Key Storage,这是Java的专利,跟OpenSSL关系不大,利用Java的一个叫"keytool"的工具,可以将PFX转为JKS,当然了,keytool也能直接生成JKS,不过在此就不多表了.

### 证书编码的转换

**PEM转为DER** openssl x509 -in cert.crt -outform der -out cert.der

**DER转为PEM** openssl x509 -in cert.crt -inform der -outform pem -out cert.pem

(提示:要转换KEY文件也类似,只不过把x509换成rsa,要转CSR的话,把x509换成req...)

### 使用openssl生成秘钥

生成2048位RSA秘钥，使用3des加密秘钥文件private.pem

> openssl genrsa -des3 -out private.pem 2048

导出公钥，默认为PKCS#8结构

> openssl rsa -in private.pem -outform PEM -pubout -out public.pem

导出PKCS#1结构的公钥，注意openssl版本，老版本可能不支持

> openssl rsa -in private.pem -outform DER **-RSAPublicKey_out** -out public_pcks1.cer

导出无加密保护的私钥

> openssl rsa -in private.pem -out private_unencrypted.pem -outform PEM
> 


### 参考资料  
[秘钥格式](https://medium.com/@oyrxx/rsa%E7%A7%98%E9%92%A5%E4%BB%8B%E7%BB%8D%E5%8F%8Aopenssl%E7%94%9F%E6%88%90%E5%91%BD%E4%BB%A4-d3fcc689513f)

[ASN.1 key structures in DER and PEM](https://tls.mbed.org/kb/cryptography/asn1-key-structures-in-der-and-pem)

[[那些证书相关的玩意儿(SSL,X.509,PEM,DER,CRT,CER,KEY,CSR,P12等)](https://www.cnblogs.com/guogangj/p/4118605.html)](https://www.cnblogs.com/guogangj/p/4118605.html)

[openssl之PEM系列之1---PEM编码文件结构介绍](https://groups.google.com/forum/#!topic/cn.bbs.comp.security/90Z0inkJ3lo)