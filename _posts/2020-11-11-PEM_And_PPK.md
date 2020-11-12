---
layout: post
title:  ".pem .ppk 差别"
date:   2020-11-11 12:00:00
tags:	technology
myTag:	aws
headimg: demo.png

---

### 什么是PEM文件

PEM（Private Enhanced Mail）是一个Base64编码的文本文件，包含了：
+ `private key`，
+ `CA server certificate`
+ `trust chain additonal certificates (root certificate, intermediate certificate)`

下面是一个例子：

```
# Private key
-----BEGIN PRIVATE KEY-----
MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQDBj08sp5++4anG
cmQxJjAkBgNVBAoTHVByb2dyZXNzIFNvZnR3YXJlIENvcnBvcmF0aW9uMSAwHgYD
VQQDDBcqLmF3cy10ZXN0LnByb2dyZXNzLmNvbTCCASIwDQYJKoZIhvcNAQEBBQAD
...
bml6YXRpb252YWxzaGEyZzIuY3JsMIGgBggrBgEFBQcBAQSBkzCBkDBNBggrBgEF
BQcwAoZBaHR0cDovL3NlY3VyZS5nbG9iYWxzaWduLmNvbS9jYWNlcnQvZ3Nvcmdh
z3P668YfhUbKdRF6S42Cg6zn
-----END PRIVATE KEY-----

# Server CA certificate
-----BEGIN CERTIFICATE-----
MIIFaDCCBFCgAwIBAgISESHkvZFwK9Qz0KsXD3x8p44aMA0GCSqGSIb3DQEBCwUA
VQQDDBcqLmF3cy10ZXN0LnByb2dyZXNzLmNvbTCCASIwDQYJKoZIhvcNAQEBBQAD
ggEPADCCAQoCggEBAMGPTyynn77hqcYnjWsMwOZDzdhVFY93s2OJntMbuKTHn39B
...
bml6YXRpb252YWxzaGEyZzIuY3JsMIGgBggrBgEFBQcBAQSBkzCBkDBNBggrBgEF
BQcwAoZBaHR0cDovL3NlY3VyZS5nbG9iYWxzaWduLmNvbS9jYWNlcnQvZ3Nvcmdh
bml6YXRpb252YWxzaGEyZzJyMS5jcnQwPwYIKwYBBQUHMAGGM2h0dHA6Ly9vY3Nw
lffygD5IymCSuuDim4qB/9bh7oi37heJ4ObpBIzroPUOthbG4gv/5blW3Dc=
-----END CERTIFICATE-----

# Trust chain intermediate certificate
-----BEGIN CERTIFICATE-----
MIIEaTCCA1GgAwIBAgILBAAAAAABRE7wQkcwDQYJKoZIhvcNAQELBQAwVzELMAkG
C33JiJ1Pi/D4nGyMVTXbv/Kz6vvjVudKRtkTIso21ZvBqOOWQ5PyDLzm+ebomchj
SHh/VzZpGhkdWtHUfcKc1H/hgBKueuqI6lfYygoKOhJJomIZeg0k9zfrtHOSewUj
...
dHBzOi8vd3d3Lmdsb2JhbHNpZ24uY29tL3JlcG9zaXRvcnkvMDMGA1UdHwQsMCow
KKAmoCSGImh0dHA6Ly9jcmwuZ2xvYmFsc2lnbi5uZXQvcm9vdC5jcmwwPQYIKwYB
K1pp74P1S8SqtCr4fKGxhZSM9AyHDPSsQPhZSZg=
-----END CERTIFICATE-----

# Trust chain root certificate
-----BEGIN CERTIFICATE-----
MIIDdTCCAl2gAwIBAgILBAAAAAABFUtaw5QwDQYJKoZIhvcNAQEFBQAwVzELMAkG
YWxTaWduIG52LXNhMRAwDgYDVQQLEwdSb290IENBMRswGQYDVQQDExJHbG9iYWxT
aWduIFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDaDuaZ
...
jc6j40+Kfvvxi4Mla+pIH/EqsLmVEQS98GPR4mdmzxzdzxtIK+6NiY6arymAZavp
38NflNUVyRRBnMRddWQVDf9VMOyGj/8N7yy5Y0b2qvzfvGn9LhJIZJrglfCm7ymP
HMUfpIBvFSDJ3gyICh3WZlXi/EjJKSZp4A==
-----END CERTIFICATE-----
```

### PEM文件作用

既然PEM文件带有私钥和一系列从CA开始的证书，那么PEM文件可以生成它对应的证书（证书中包含公钥，公钥的数字签名）。

`PrivateKey --> Certificate(PublicKey)`


### 数字签名 Digital Signature

当发送方想给接收方发送一些文件，且不希望这些文件在传输过程中被篡改，那么发送方可以使用自己的私钥对文件进行签名（加密）；
待接收方接收到文件后，使用发送方已经公开在网络上的公钥进行验签（解密），那么就可以确认文件是有发送方发送且未经过篡改。

私钥签名——实际上就是对整个文件做一次摘要（SHA，MD5等），并把摘要使用私钥加密，并附加在原本信息中发送；
由于接收方具有公钥，它可以对传输获得的摘要信息进行解密，并对传输获得的原文件做一次摘要，对比两次摘要的结果，判断文件是否在传输中受到篡改。

### 什么是PPK文件

Putty Private Key从名字上我们知道这是一个包含私钥的文件。PPK是Windows平台配合SSH客户端Putty使用的私钥文件，本质上和PEM并无不同，区别仅是文件格式和使用平台。一般来说PEM和PPK是可以互相转换的。


### Reference

+ [ssh密钥 .ppk、.pub和.pem](https://liyangkai.com/2019110201/)
+ [PEM file format.](https://documentation.progress.com/output/DataDirect/hybridpipeinstall/index.html#page/install/pem-file-format.html)
+ [What is the difference between the .pem and .pub and non suffixed ssh credentials files? StackExchange.](https://superuser.com/questions/527218/what-is-the-difference-between-the-pem-and-pub-and-non-suffixed-ssh-credential)
+ [什么是数字签名和证书？](https://www.jianshu.com/p/9db57e761255)
+ [RSA公钥、私钥、签名和验签](https://www.jianshu.com/p/3331467d139f)
