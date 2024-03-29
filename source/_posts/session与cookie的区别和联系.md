---
title: session与cookie的区别和联系
date: 2018-03-09 17:34:42
tags:
---

## Session的概念

Session 是存放在服务器端的，类似于Session结构来存放用户数据，当浏览器 第一次发送请求时，服务器自动生成了一个Session和一个Session ID用来唯一标识这个Session，并将其通过响应发送到浏览器。当浏览器第二次发送请求，会将前一次服务器响应中的Session ID放在请求中一并发送到服务器上，服务器从请求中提取出Session ID，并和保存的所有Session ID进行对比，找到这个用户对应的Session。

一般情况下，服务器会在一定时间内（默认30分钟）保存这个 Session，过了时间限制，就会销毁这个Session。在销毁之前，程序员可以将用户的一些数据以Key和Value的形式暂时存放在这个 Session中。当然，也有使用数据库将这个Session序列化后保存起来的，这样的好处是没了时间的限制，坏处是随着时间的增加，这个数据 库会急速膨胀，特别是访问量增加的时候。一般还是采取前一种方式，以减轻服务器压力。

## Session的客户端实现形式

一般浏览器提供了两种方式来保存，还有一种是程序员使用html隐藏域的方式自定义实现：

[1] 使用Cookie来保存，这是最常见的方法，本文“记住我的登录状态”功能的实现正式基于这种方式的。服务器通过设置Cookie的方式将Session ID发送到浏览器。如果我们不设置这个过期时间，那么这个Cookie将不存放在硬盘上，当浏览器关闭的时候，Cookie就消失了，这个Session ID就丢失了。如果我们设置这个时间为若干天之后，那么这个Cookie会保存在客户端硬盘中，即使浏览器关闭，这个值仍然存在，下次访问相应网站时，同 样会发送到服务器上。

[2] 使用URL附加信息的方式，也就是像我们经常看到JSP网站会有aaa.jsp?JSESSIONID=*一样的。这种方式和第一种方式里面不设置Cookie过期时间是一样的。

[3] 第三种方式是在页面表单里面增加隐藏域，这种方式实际上和第二种方式一样，只不过前者通过GET方式发送数据，后者使用POST方式发送数据。但是明显后者比较麻烦。


## cookie与session的区别

cookie数据保存在客户端，session数据保存在服务器端。

简单的说，当你登录一个网站的时候，如果web服务器端使用的是session,那么所有的数据都保存在服务器上面，客户端每次请求服务器的时候会发送 当前会话的sessionid，服务器根据当前sessionid判断相应的用户数据标志，以确定用户是否登录，或具有某种权限。由于数据是存储在服务器 上面，所以你不能伪造，但是如果你能够获取某个登录用户的sessionid，用特殊的浏览器伪造该用户的请求也是能够成功的。sessionid是服务 器和客户端链接时候随机分配的，一般来说是不会有重复，但如果有大量的并发请求，也不是没有重复的可能性，我曾经就遇到过一次。登录某个网站，开始显示的 是自己的信息，等一段时间超时了，一刷新，居然显示了别人的信息。

如果浏览器使用的是 cookie，那么所有的数据都保存在浏览器端，比如你登录以后，服务器设置了 cookie用户名(username),那么，当你再次请求服务器的时候，浏览器会将username一块发送给服务器，这些变量有一定的特殊标记。服 务器会解释为 cookie变量。所以只要不关闭浏览器，那么 cookie变量便一直是有效的，所以能够保证长时间不掉线。如果你能够截获某个用户的 cookie变量，然后伪造一个数据包发送过去，那么服务器还是认为你是合法的。所以，使用 cookie被攻击的可能性比较大。如果设置了的有效时间，那么它会将 cookie保存在客户端的硬盘上，下次再访问该网站的时候，浏览器先检查有没有 cookie，如果有的话，就读取该 cookie，然后发送给服务器。如果你在机器上面保存了某个论坛 cookie，有效期是一年，如果有人入侵你的机器，将你的 cookie拷走，然后放在他的浏览器的目录下面，那么他登录该网站的时候就是用你的的身份登录的。所以 cookie是可以伪造的。当然，伪造的时候需要主意，直接copy cookie文件到 cookie目录，浏览器是不认的，他有一个index.dat文件，存储了cookie文件的建立时间，以及是否有修改，所以你必须先要有该网站的 cookie文件，并且要从保证时间上骗过浏览器。

Session是由应用服务器维持的一个服务器端的存储空间，用户在连接服务器时，会由服务器生成一个唯一的SessionID,用该SessionID 为标识符来存取服务器端的Session存储空间。而SessionID这一数据则是保存到客户端，用Cookie保存的，用户提交页面时，会将这一 SessionID提交到服务器端，来存取Session数据。这一过程，是不用开发人员干预的。所以一旦客户端**禁用Cookie**，那么Session也会失效。

服务器也可以**通过URL重写的方式来传递SessionID的值，因此不是完全依赖Cookie**。如果客户端Cookie禁用，则服务器可以自动通过重写URL的方式来保存Session的值，并且这个过程对程序员透明。

可以试一下，即使不写Cookie，在使用request.getCookies();取出的Cookie数组的长度也是1，而这个Cookie的名字就是JSESSIONID，还有一个很长的二进制的字符串，是SessionID的值。


## Session与Cookie的联系：
Cookies是属于Session对象的一种。但有不同，Cookies不会占服务器资源，是存在客服端内存或者一个cookie的文本文件中；而“Session”则会占用服务器资源。所以，尽量不要使用Session，而使用Cookies。但是我们一般认为cookie是不可靠的，session是可靠地，但是目前很多著名的站点也都以来cookie。有时候为了解决禁用cookie后的页面处理，通常采用url重写技术，调用session中大量有用的方法从session中获取数据后置入页面。

Cookies与Session的应用场景：

Cookies的安全性能一直是倍受争议的。虽然Cookies是保存在本机上的，但是其信息的完全可见性且易于本地编辑性，往往可以引起很多的安全问题。所以Cookies到底该不该用，到底该怎样用，就有了一个需要给定的底线。

先来看看，网站的敏感数据有哪些。

登陆验证信息。一般采用Session(“Logon”)＝true or false的形式。
用户的各种私人信息，比如姓名等，某种情况下，需要保存在Session里
需要在页面间传递的内容信息，比如调查工作需要分好几步。每一步的信息都保存在Session里，最后在统一更新到数据库。

当然还会有很多，这里列举一些比较典型的
假如，一个人孤僻到不想碰Session，因为他认为，如果用户万一不小心关闭了浏览器，那么之前保存的数据就全部丢失了。所以，他出于好意，决定把这些用Session的地方，都改成用Cookies来存储，这完全是可行的，且基本操作和用Session一模一样。那么，下面就针对以上的3个典型例子，做一个分析
很显然，只要某个有意非法入侵者，知道该网站验证登陆信息的Session变量是什么，那么他就可以事先编辑好该Cookies，放入到Cookies目录中，这样就可以顺利通过验证了。这是不是很可怕？
Cookies完全是可见的，即使程序员设定了Cookies的生存周期（比如只在用户会话有效期内有效），它也是不安全的。假设，用户忘了关浏览器 或者一个恶意者硬性把用户给打晕，那用户的损失将是巨大的。
这点如上点一样，很容易被它人窃取重要的私人信息。但，其还有一个问题所在是，可能这些数据信息量太大，而使得Cookies的文件大小剧增。这可不是用户希望所看到的。

显然，Cookies并不是那么一块好啃的小甜饼。但，Cookies的存在，当然有其原因。它给予程序员更多发挥编程才能的空间。所以，使用Cookies改有个底线。这个底线一般来说，遵循以下原则。
不要保存私人信息。
任何重要数据，最好通过加密形式来保存数据（最简单的可以用URLEncode，当然也可以用完善的可逆加密方式，遗憾的是，最好不要用md5来加密）。
是否保存登陆信息，需有用户自行选择。
长于10K的数据，不要用到Cookies。
也不要用Cookies来玩点让客户惊喜的小游戏。


## cookie最典型的应用是：
（一）：判断用户是否登陆过网站，以便下次登录时能够直接登录。如果我们删除cookie，则每次登录必须从新填写登录的相关信息。

（二）：另一个重要的应用是“购物车”中类的处理和设计。用户可能在一段时间内在同一家网站的不同页面选择不同的商品，可以将这些信息都写入cookie，在最后付款时从cookie中提取这些信息，当然这里面有了安全和性能问题需要我们考虑了。

