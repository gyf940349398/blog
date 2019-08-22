---
title: 十问Https
date: 2019-08-22 17:31:53
categories: 计算机网络
tags: Https
---

# 十问Https
## 前言
之前一直对Https方面的知识似懂非懂，这两天抽空进行了一次较深入的学习，为了加深巩固，特意总结出一些问题，已验证是否真正理解，文中如有错误，欢迎指正~

## 问题
### 1.Http有哪些缺点？
1. 通信使用明文传输，不加密，可能被监听
2. 无法验证通信方的身份（Http不具备验证机制），可能伪造
3. 内容完整性无法保证，可能被篡改

### 2.如何解决明文传输问题？
加密，对通信加密（TLS）、对内容加密（SSL）

### 3.如何验证通信方身份？
采用认证机制，通过验证通信方的证书来确认身份，证书由可信任的第三方数字证书机构签发

### 4.如何保证内容完整性？
通信和内容得到了加密，并且加密所用的密钥是安全不泄露的，那么就能保证内容的完整性，因为其他人无法解密

### 5.内容加密都有哪些加密方式？
1. 共享密钥加密，又称*对称密钥加密*，加解密使用同一个密钥，并且密钥采用明文方式跟随内容传输，所以很容易被中间人获取，从而破解内容，安全性较低，但是处理速度快，效率高
2. 公开密钥加密，又称*非对称密钥加密*，一对密钥（公钥&私钥），使用私钥加密的内容，只有公钥才能解密，反之亦然；公钥可以给任何人，私钥由服务端自己保存好；虽然安全性较高，但处理速度慢，效率低，而且公钥使用明文传输，容易被中间人获取并篡改，也就是所谓的*”中间人攻击“*。

### 6.Https采用哪种加密方式？
混合加密方式：先使用公开密钥加密方式来传递公钥，公钥加密生成密钥，然后再使用这个密钥以共享密钥加密的方式进行后续的内容传输。那么这种所谓的混合加密方式安全吗？答案是：*不安全*，容易遭到中间人攻击。

### 7.何为中间人攻击？
中间人攻击，即客户端与服务端通信时，中间的代理服务器、运营商、网关等都有可能对通信进行监听、拦截和篡改，具体过程大致如下：
1. 客户端向服务端发起建立安全通信的请求
2. 服务端将公钥以*明文方式*发送给客户端
3. 中间人将公钥保存下来，并生成一个假的公钥给客户端
4. 客户端使用假的公钥生成随机数发送给服务端
5. 中间人使用假的公钥解密得到随机数，然后再生成一个假的随机数，并使用那个真的服务端公钥加密，然后发送给服务端，使用真的公钥加密假的随机数目的是确保服务端能够用私钥解密，否则服务端会解密失败
6. 服务端使用私钥解密得到随机数，并用这个随机数推导出密钥，推到方式是公开的，也就是说只要有了随机数也就有了密钥，那么此时除了客户端、服务端有这个随机数，中间人也有，所以在后续的通信中，中间人就可以拿这个密钥解密数据包，从而篡改内容

概括起来就是：*中间人用假公钥替换了服务端发送给客户端的真公钥，导致客户端使用假的公钥加密随机数并发给服务端，中间人从中解密得到随机数，这样就能推导出后续加密数据包所用到的密钥，之后客户端与服务端的通信就再无秘密可言*

### 8.如何防止中间人攻击？
其实只要在服务端发送公钥这个阶段，公钥不被中间人获取篡改，那么问题就解决了，所以如何才能保证公钥不被中间人获取呢，方法就是*用数字证书对公钥进行加密*

### 9.证书如何加密公钥？以及如何证明证书不是伪造的？
首先，颁发数字证书的是可信任的第三方机构（简称CA），这是前提；服务端获得的证书包含服务端自己的公钥，并且是用CA的私钥加密过的，这个证书只能由CA的公钥进行解密验证。

目前市场上的浏览器都会事先内置一些CA的公钥，目的就是避免公钥在传输过程中泄露，所以与其就不传输，直接内置

当浏览器接收到服务端的证书时，先进行验证，而不是直接使用；浏览器使用CA的公钥验证证书是否有效，如果验证通过，就证明证书中的公钥也是真实有效的，然后就可以使用这个公钥了；在这个过程中，中间人无法解密（因为CA的公钥是内置在浏览器中的，并没有在通信中携带），也就无法得到公钥，进而即使生成假公钥也是多余的，因为即使生成了，给到客户端也是验证不通过的

### 10.还有哪些要补充的？
1. 服务端也可以要求验证客户端的证书，但是客户端要想得到证书，需要付费购买，并且要求每个用户都安装，这是不现实的，存在学习成本
2. 生成*共享密钥加密方式*使用的*密钥*时，无论是采用*DH*算法，还是*RSA*算法，目的都只有一个，那就是*生成复杂的安全性高的密钥*

## 参考
1. 《图解Http》第7章：确保Web安全的Https
2.  [HTTPS可以防止中间人篡改内容吗？ - 知乎](https://www.zhihu.com/question/65464646)