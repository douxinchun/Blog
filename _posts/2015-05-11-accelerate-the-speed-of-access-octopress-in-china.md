---
layout: post
title: "加快Octopress国内访问速度"
date: 2015-05-11 14:13:23 +0800
comments: true
categories: octopress
---  

##Overview  
本文最主要引自[加快octopress国内访问速度](http://www.chanjar.me/blog/2014/06/29/jia-kuai-octopressguo-nei-fang-wen-su-du/)和[Octopress加速Google字体渲染](http://imxylz.com/blog/2013/09/22/move-google-fonts-to-local-server/),以下内容是在这两篇博文的指导上结合自己实践经验的总结.  

<!--more-->
在github中搭建好了Octopress后,访问速度非常的慢,这是引文Octopress使用了Google API的CDN服务和Google Fonts.Google的CDN访问速度在国外的网络环境下是非常快的,但是在天朝,非常非常的慢,尤其是使用了https来访问.  

于是,为了加快访问速度,我们只好把Google的web fonts缓存到本地的服务器并更改jQuery的cdn服务.

###1.把jquery的cdn服务改成microsoft的 
编辑文件 source/_includes/head.html  找到下图注释掉的部分,替换成 <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js"></script>

{%codeblock  %}
  <!--<script src="//ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>-->
  <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js"></script>
{%endcodeblock%}

###2.缓存google fonts (将PT Serif和PT Sans缓存到本地) 
打开 source/_includes/custom/head.html  
{%codeblock%}
<!--Fonts from Google"s Web font directory at http://google.com/webfonts -->
<link href="//fonts.googleapis.com/css?family=PT+Serif:regular,italic,bold,bolditalic" rel="stylesheet" type="text/css">
<link href="//fonts.googleapis.com/css?family=PT+Sans:regular,italic,bold,bolditalic" rel="stylesheet" type="text/css">
{%endcodeblock%}  
将 https://fonts.googleapis.com/css?family=PT+Serif:regular,italic,bold,bolditalic 
以及 https://fonts.googleapis.com/css?family=PT+Sans:regular,italic,bold,bolditalic 中的内容复制到本地,并在 source/stylesheets 下新建pt_sans.css和pt_serif.css文件,文件部分内容如下:  
{%codeblock%}
/* cyrillic-ext */
@font-face {
  font-family: 'PT Serif';
  font-style: normal;
  font-weight: 400;
  src: local('PT Serif'), local('PTSerif-Regular'), url(https://fonts.gstatic.com/s/ptserif/v8/5hX15RUpPERmeybVlLQEWBkAz4rYn47Zy2rvigWQf6w.woff2) format('woff2');
  unicode-range: U+0460-052F, U+20B4, U+2DE0-2DFF, U+A640-A69F;
}
/* cyrillic */
@font-face {
  font-family: 'PT Serif';
  font-style: normal;
  font-weight: 400;
  src: local('PT Serif'), local('PTSerif-Regular'), url(https://fonts.gstatic.com/s/ptserif/v8/fU0HAfLiPHGlZhZpY6M7dBkAz4rYn47Zy2rvigWQf6w.woff2) format('woff2');
  unicode-range: U+0400-045F, U+0490-0491, U+04B0-04B1, U+2116;
}
/* latin-ext */
@font-face {
  font-family: 'PT Serif';
  font-style: normal;
  font-weight: 400;
  src: local('PT Serif'), local('PTSerif-Regular'), url(https://fonts.gstatic.com/s/ptserif/v8/CPRt--GVMETgA6YEaoGitxkAz4rYn47Zy2rvigWQf6w.woff2) format('woff2');
  unicode-range: U+0100-024F, U+1E00-1EFF, U+20A0-20AB, U+20AD-20CF, U+2C60-2C7F, U+A720-A7FF;
}
...
{%endcodeblock%}  
注意,此处访问这两个链接可能需要翻墙.
新建一个目录 source/fonts 把字体解压缩到这个目录下.  
{%codeblock%}
douxinchundeiMac:fonts douxinchun$ pwd
/Users/douxinchun/octopress/source/fonts
douxinchundeiMac:fonts douxinchun$ tree
.
├── pt_sans
│   ├── 0XxGQsSc1g4rdRdjJKZrNAzyDMXhdD8sAj6OAJTFsBI.woff2
│   ├── 7dSh6BcuqDLzS2qAASIeuoX0hVgzZQUfRDuZrPvH3D8.woff2
│   ├── BJVWev7_auVaQ__OU8Qih1KPGs1ZzpMvnHX-7fPOuAc.woff2
│   ├── CWlc_g68BGYDSGdpJvpktgLUuEpTyoUstqEm5AMlJo4.woff2
│   ├── DVKQJxMmC9WF_oplMzlQqYX0hVgzZQUfRDuZrPvH3D8.woff2
│   ├── GpWpM_6S4VQLPNAQ3iWvVYX0hVgzZQUfRDuZrPvH3D8.woff2
│   ├── PIPMHY90P7jtyjpXuZ2cLJBw1xU1rKptJj_0jans920.woff2
│   ├── fhNmDCnjccoUYyU4ZASaLVKPGs1ZzpMvnHX-7fPOuAc.woff2
│   ├── g46X4VH_KHOWAAa-HpnGPgsYbbCjybiHxArTLjt7FRU.woff2
│   ├── hpORcvLZtemlH8gI-1S-7gsYbbCjybiHxArTLjt7FRU.woff2
│   ├── kTYfCWJhlldPf5LnG4ZnHAsYbbCjybiHxArTLjt7FRU.woff2
│   ├── lILlYDvubYemzYzN7GbLkA7aC6SjiAOpAWOKfJDfVRY.woff2
│   ├── lILlYDvubYemzYzN7GbLkBampu5_7CjHW5spxoeN3Vs.woff2
│   ├── lILlYDvubYemzYzN7GbLkBdwxCXfZpKo5kWAx_74bHs.woff2
│   ├── lILlYDvubYemzYzN7GbLkIjoYw3YTyktCCer_ilOlhE.woff2
│   └── oysROHFTu1eTZ74Hcf8V-VKPGs1ZzpMvnHX-7fPOuAc.woff2
└── pt_serif
    ├── 03aPdn7fFF3H6ngCgAlQzAzyDMXhdD8sAj6OAJTFsBI.woff2
    ├── 3Nwg9VzlwLXPq3fNKwVRMAsYbbCjybiHxArTLjt7FRU.woff2
    ├── 5hX15RUpPERmeybVlLQEWBkAz4rYn47Zy2rvigWQf6w (1).woff2
    ├── CPRt--GVMETgA6YEaoGitxkAz4rYn47Zy2rvigWQf6w.woff2
    ├── Foydq9xJp--nfYIx2TBz9TrEaqfC9P2pvLXik1Kbr9s.woff2
    ├── Foydq9xJp--nfYIx2TBz9WaVI6zN22yiurzcBKxPjFE.woff2
    ├── Foydq9xJp--nfYIx2TBz9ZsnFT_2ovhuEig4Dh-CBQw.woff2
    ├── Foydq9xJp--nfYIx2TBz9bllaL-ufMOTUcv7jfgmuJg.woff2
    ├── I-OtoJZa3TeyH6D9oli3iXYhjbSpvc47ee6xR_80Hnw.woff2
    ├── O_WhD9hODL16N4KLHLX7xQsYbbCjybiHxArTLjt7FRU.woff2
    ├── QABk9IxT-LFTJ_dQzv7xpF4sYYdJg5dU2qzJEVSuta0.woff2
    ├── QABk9IxT-LFTJ_dQzv7xpIgp9Q8gbYrhqGlRav_IXfk.woff2
    ├── QABk9IxT-LFTJ_dQzv7xpKE8kM4xWR1_1bYURRojRGc.woff2
    ├── QABk9IxT-LFTJ_dQzv7xpPZraR2Tg8w2lzm7kLNL0-w.woff2
    ├── b31S45a_TNgaBApZhTgE6AsYbbCjybiHxArTLjt7FRU.woff2
    └── fU0HAfLiPHGlZhZpY6M7dBkAz4rYn47Zy2rvigWQf6w.woff2

2 directories, 32 files
douxinchundeiMac:fonts douxinchun$
{%endcodeblock%}  

将文件pt_sans.css和pt_serif.css的web引用修改为本地引用,文件部分内容如下:
{%codeblock%}
/* cyrillic-ext */
@font-face {
  font-family: 'PT Sans';
  font-style: normal;
  font-weight: 400;
  src: local('PT Sans'), local('PTSans-Regular'), url(/fonts/pt_sans/fhNmDCnjccoUYyU4ZASaLVKPGs1ZzpMvnHX-7fPOuAc.woff2) format('woff2');
  unicode-range: U+0460-052F, U+20B4, U+2DE0-2DFF, U+A640-A69F;
}
/* cyrillic */
@font-face {
  font-family: 'PT Sans';
  font-style: normal;
  font-weight: 400;
  src: local('PT Sans'), local('PTSans-Regular'), url(/fonts/pt_sans/BJVWev7_auVaQ__OU8Qih1KPGs1ZzpMvnHX-7fPOuAc.woff2) format('woff2');
  unicode-range: U+0400-045F, U+0490-0491, U+04B0-04B1, U+2116;
}
{%endcodeblock%}  

编辑 source/_includes/custom/head.html 为
{%codeblock%}
<!--Fonts from Google"s Web font directory at http://google.com/webfonts -->
<link href="/stylesheets/pt_serif.css" rel="stylesheet" type="text/css">
<link href="/stylesheets/pt_sans.css" rel="stylesheet" type="text/css">
{%endcodeblock%}  

部署访问,速度果然快了很多.



