---
title: Http/Https的区别
date: 2019-12-11 23:11:46
categories: Network
tags:
---

### 1.get和post请求方法的区别？
get在做后退按钮或者刷新的时候不会有什么影响，但是post数据就会被重新提交
get可以被收藏为书签，而post请求不可能被收藏为书签
安全性而言，get比post相比较而言要差一些，get请求的数据会显示在url中，而post则不会，对于一些比较敏感的信息一般会采用post请求；

HTTPS在HTTP的基础上加入了SSL协议，SSL依靠证书来验证服务器的身份，并为浏览器和服务器之间的通信加密。
https的工作原理：客户端使用https的url与服务器建立ssl连接，服务器发给客户端一些证书信息，其中包含公钥，客户端与浏览器开始协商ssl连接的等级，也就是信息加密的等级，然后客户端的浏览器建立会话密钥，利用网站的公钥将密钥解密，传送给网站。web服务器利用自己的私钥解密出会话密钥，完成通信。
### 2.HTTPS的优点
　　尽管HTTPS并非绝对安全，掌握根证书的机构、掌握加密算法的组织同样可以进行中间人形式的攻击，但HTTPS仍是现行架构下最安全的解决方案，主要有以下几个好处：
　　（1）使用HTTPS协议可认证用户和服务器，确保数据发送到正确的客户机和服务器；
　　（2）HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，要比http协议安全，可防止数据在传输过程中不被窃取、改变，确保数据的完整性。
　　（3）HTTPS是现行架构下最安全的解决方案，虽然不是绝对安全，但它大幅增加了中间人攻击的成本。
　　（4）谷歌曾在2014年8月份调整搜索引擎算法，并称“比起同等HTTP网站，采用HTTPS加密的网站在搜索结果中的排名将会更高”。
### 3.HTTPS的缺点
　　虽然说HTTPS有很大的优势，但其相对来说，还是存在不足之处的：
　　（1）HTTPS协议握手阶段比较费时，会使页面的加载时间延长近50%，增加10%到20%的耗电；
　　（2）HTTPS连接缓存不如HTTP高效，会增加数据开销和功耗，甚至已有的安全措施也会因此而受到影响；
　　（3）SSL证书需要钱，功能越强大的证书费用越高，个人网站、小网站没有必要一般不会用。
　   （4）SSL证书通常需要绑定IP，不能在同一IP上绑定多个域名，IPv4资源不可能支撑这个消耗。
　　（5）HTTPS协议的加密范围也比较有限，在黑客攻击、拒绝服务攻击、服务器劫持等方面几乎起不到什么作用。最关键的，SSL证书的信用链体系并不安全，特别是在某些国家可以控制CA根证书的情况下，中间人攻击一样可行。