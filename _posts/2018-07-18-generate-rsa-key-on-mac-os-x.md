---
layout: post
title: "Mac OS X 下生成RSA密钥对"
date: 2018-07-18 15:15:18 +d0800
comments: true
categories: [mac os x, terminal, rsa]
---

Mac自带OpenSSL,可以利用openssl来生成RSA密钥对

1.生成私钥,1024指密钥的长度

``` bash
➜  test openssl genrsa -out private_key.pem 1024
Generating RSA private key, 1024 bit long modulus
.......................................................................++++++
.......................................................................++++++
e is 65537 (0x10001)
➜  test cat private_key.pem 
-----BEGIN RSA PRIVATE KEY-----
MIICXQIBAAKBgQDJrT1W3ZyBAYIMNb0XDBQTIGw4TpbrLelQ/K6Yl6abciAUAZNn
j5EHlFhMvpe3iHU0xPIcTM0IM9ZnMjecikHC4lBJ0JmXEsHf4j6Cf2+KRFHTaxPP
hqf65wiGvduTf70xOCvaPeyYVu4x69jm2rtxOeVvF1PJSrV+ZDCoDmYD3QIDAQAB
AoGAToUwmJ13zZJ0u6RAlrSRLFE3UUTn5XDeojV/FNIWf/cTHjbu2SdAZB8Rse+S
ylZKq9zyFqqgOU1VcKBQnpYFu9XFvpoP/xGC/T99MN3chBaQY4wY80FM8NEjWZbP
ZPTczE/HypE+J14/0i0x6jujhFiAAmcE/1ivRzRGvo6qC9ECQQDmcki4jO4VTju7
+WWINatj36pPn/JVUFH8Tqw98tQLg5VMPFfPNsl9L7CSqACCFvl1wiERuxfunRB0
9dNHb4sHAkEA4ApECRgqo+/7Smd2WTa4hSQ+aYs6J7+9e8zjgfIuYBn5ONOsuIuV
VntHyhSc2Xzbp1wkakCzTy/Tyw02kGCs+wJBAMFLHPpHo7AVRf9+yo48zjzgr99H
/yFWVN54MvtnQjtCLKmcd97kSo+Jv+bTqlFz6dy/b7OKpiFMdzBTvdtOkWMCQQC6
21ULUMCfopQv5kLq/ZzATw5O8PQ8Gstq6eQGiXrsZD1cjA9Oi/yt+HxTqwV2z5BT
8aHdjMEAlp9Kh2au3DLpAkB+koitVpjxmWyzQ/Sl4Xa843oR+qneofZZ3m9johtn
+7NQu3JXI1xBckxmZ4DtODtC7MUIqlOEej5OT9QVwljv
-----END RSA PRIVATE KEY-----

```

2.生成公钥

``` bash
➜  test openssl rsa -in private_key.pem -pubout -out public_key.pem
writing RSA key
➜  test cat public_key.pem 
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDJrT1W3ZyBAYIMNb0XDBQTIGw4
TpbrLelQ/K6Yl6abciAUAZNnj5EHlFhMvpe3iHU0xPIcTM0IM9ZnMjecikHC4lBJ
0JmXEsHf4j6Cf2+KRFHTaxPPhqf65wiGvduTf70xOCvaPeyYVu4x69jm2rtxOeVv
F1PJSrV+ZDCoDmYD3QIDAQAB
-----END PUBLIC KEY-----
```

3.使用pkcs8命令转换私钥格式 PKCS标准

``` bash
➜  test openssl pkcs8 -topk8 -inform PEM -in private_key.pem -outform PEM -nocrypt
-----BEGIN PRIVATE KEY-----
MIICdwIBADANBgkqhkiG9w0BAQEFAASCAmEwggJdAgEAAoGBAMmtPVbdnIEBggw1
vRcMFBMgbDhOlust6VD8rpiXpptyIBQBk2ePkQeUWEy+l7eIdTTE8hxMzQgz1mcy
N5yKQcLiUEnQmZcSwd/iPoJ/b4pEUdNrE8+Gp/rnCIa925N/vTE4K9o97JhW7jHr
2Obau3E55W8XU8lKtX5kMKgOZgPdAgMBAAECgYBOhTCYnXfNknS7pECWtJEsUTdR
ROflcN6iNX8U0hZ/9xMeNu7ZJ0BkHxGx75LKVkqr3PIWqqA5TVVwoFCelgW71cW+
mg//EYL9P30w3dyEFpBjjBjzQUzw0SNZls9k9NzMT8fKkT4nXj/SLTHqO6OEWIAC
ZwT/WK9HNEa+jqoL0QJBAOZySLiM7hVOO7v5ZYg1q2Pfqk+f8lVQUfxOrD3y1AuD
lUw8V882yX0vsJKoAIIW+XXCIRG7F+6dEHT100dviwcCQQDgCkQJGCqj7/tKZ3ZZ
NriFJD5pizonv717zOOB8i5gGfk406y4i5VWe0fKFJzZfNunXCRqQLNPL9PLDTaQ
YKz7AkEAwUsc+kejsBVF/37KjjzOPOCv30f/IVZU3ngy+2dCO0IsqZx33uRKj4m/
5tOqUXPp3L9vs4qmIUx3MFO9206RYwJBALrbVQtQwJ+ilC/mQur9nMBPDk7w9Dwa
y2rp5AaJeuxkPVyMD06L/K34fFOrBXbPkFPxod2MwQCWn0qHZq7cMukCQH6SiK1W
mPGZbLND9KXhdrzjehH6qd6h9lneb2OiG2f7s1C7clcjXEFyTGZngO04O0LsxQiq
U4R6Pk5P1BXCWO8=
```



