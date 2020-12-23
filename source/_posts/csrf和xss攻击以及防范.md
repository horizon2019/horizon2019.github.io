---
title: csrf和xss攻击以及防范
date: 2019-09-12 22:22:20
tags: csrf攻击
category: web安全
---

**一.什么是 CSRF?** 

CSRF，全名：Cross site Request Forgery（跨站域请求伪造）。一般的攻击方式是，通过请求伪造盗取用户的 cookie 信息，进而进行操作。

**原理：**首先用户 A 请求登陆了服务器 B，这时服务器 B 响应了用户 A，并且会返回唯一标识的该用户的 cookie 信息。
用户 A 在未退出服务器 B 时（即仍与服务器 B 保持会话状态），访问了带有恶意脚本的服务器 C，服务器 C 响应给用户 A 一个恶意页面，并且通过恶意脚本伪装用户 A 向服务器 B 发送请求，此时服务器 B 误以为是用户 A 请求，故响应并返回了用户 A 的 cookie 信息。服务器 C 收到用户 A 与 服务器 B 的cookie 信息后，保存起来，并利用该信息伪装成用户 A 去访问服务器 B，再进行相应的破坏。

**CSRF 漏洞产生的原因：**其主要原因是服务器 B 没有对请求的发起源进行合理的检验，即不加分析地认为请求者一定是正常的用户，就响应了用户信息给非法分子。（CSRF攻击是源于WEB的隐式身份验证机制！WEB的身份验证机制虽然可以保证一个请求是来自于某个用户的浏览器，但却无法保证该请求是用户批准发送的！）



**解决方法：** 

`1.验证 HTTP Referer 字段`  

Referer 的值是由浏览器提供的，虽然 HTTP 协议上有明确的要求，但是每个浏览器对于 Referer 的具体实现可能有差别，并不能保证浏览器自身没有安全漏洞。使用验证 Referer 值的方法，就是把安全性都依赖于第三方（即浏览器）来保障，从理论上来讲，这样并不安全。事实上，对于某些浏览器，比如 IE6 或 FF2，目前已经有一些方法可以篡改 。Referer 值。

`2.Cookie Hashing(所有表单都包含同一个伪随机值)：`  
```
  //首先服务器给用户颁发具有唯一用户标识的cookie信息，
　　<?php
　　　　//构造加密的Cookie信息
　　　　$value = “DefenseSCRF”;
　　　　setcookie(”cookie”, $value, time()+3600);
　　?>
```
```
   //用户在提交表单的时候，返回这个cookie值
　　<?php
　　　　$hash = md5($_COOKIE['cookie']);
　　?>
　　<form method=”POST” action=”transfer.php”>
　　　　<input type=”text” name=”toBankId”>
　　　　<input type=”text” name=”money”>
　　　　<input type=”hidden” name=”hash” value=”<?=$hash;?>”>
　　　　<input type=”submit” name=”submit” value=”Submit”>
　　</form>
```
```
//服务端进行cookie值对比，判断是不是用户本人，已经可以杜绝99%的CSRF攻击了，那还有1%呢....由于用户的Cookie很容易由于网站的XSS漏洞而被盗取，这就另外的1%。
      <?php
　　      if(isset($_POST['check'])) {
     　　      $hash = md5($_COOKIE['cookie']);
          　　 if($_POST['check'] == $hash) {
               　　 doJob();
　　           } else {
　　　　　　　　//...
          　　 }
　　      } else {
　　　　　　//...
　　      }
      ?>
```


`3.在请求地址中添加 token 并验证` 

抵御 CSRF，关键在于在请求中放入黑客所不能伪造的信息，并且该信息不存在于 cookie 之中。可以在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有 token 或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求。

3.1).先是令牌生成函数(gen_token())：

```
     <?php
     function gen_token() {
　　　　//这里我是贪方便，实际上单使用Rand()得出的随机数作为令牌，也是不安全的。
　　　　//这个可以参考我写的Findbugs笔记中的《Random object created and used only once》
          $token = md5(uniqid(rand(), true));
          return $token;
     }
```
3.2).然后是Session令牌生成函数(gen_stoken())：

```
     <?php
     　　function gen_stoken() {
　　　　　　$pToken = "";
　　　　　　if($_SESSION[STOKEN_NAME]  == $pToken){
　　　　　　　　//没有值，赋新值
　　　　　　　　$_SESSION[STOKEN_NAME] = gen_token();
　　　　　　}   
　　　　　　else{
　　　　　　　　//继续使用旧的值
　　　　　　}
     　　}
     ?>
```
3.3).WEB表单生成隐藏输入域的函数：　　

```
     <?php
　　     function gen_input() {
     　　     gen_stoken();
　　          echo “<input type=\”hidden\” name=\”" . FTOKEN_NAME . “\”
          　　     value=\”" . $_SESSION[STOKEN_NAME] . “\”> “;
     　　}
     ?>
```
3.4).WEB表单结构：

```
     <?php
          session_start();
          include(”functions.php”);
     ?>
     <form method=”POST” action=”transfer.php”>
          <input type=”text” name=”toBankId”>
          <input type=”text” name=”money”>
          <? gen_input(); ?>
          <input type=”submit” name=”submit” value=”Submit”>
     </FORM>
```
3.5).服务端核对令牌 

这种方法要比检查 Referer 要安全一些，token 可以在用户登陆后产生并放于 session 之中，然后在每次请求时把 token 从 session 中拿出，与请求中的 token 进行比对，但这种方法的难点在于如何把 token 以参数的形式加入请求。对于 GET 请求，token 将附在请求地址之后，这样 URL 就变成` http://url?csrftoken=tokenvalue。 `而对于 POST 请求来说，要在 form 的最后加上 `<input type=”hidden” name=”csrftoken” value=”tokenvalue”/>`，这样就把 token 以参数的形式加入请求了。但是，在一个网站中，可以接受请求的地方非常多，要对于每一个请求都加上 token 是很麻烦的，并且很容易漏掉，通常使用的方法就是在每次页面加载时，使用 javascript 遍历整个 dom 树，对于 dom 中所有的 a 和 form 标签后加入 token。这样可以解决大部分的请求，但是对于在页面加载之后动态生成的 html 代码，这种方法就没有作用，还需要程序员在编码时手动添加 token。
该方法还有一个缺点是难以保证 token 本身的安全。特别是在一些论坛之类支持用户自己发表内容的网站，黑客可以在上面发布自己个人网站的地址。由于系统也会在这个地址后面加上 token，黑客可以在自己的网站上得到这个 token，并马上就可以发动 CSRF 攻击。为了避免这一点，系统可以在添加 token 的时候增加一个判断，如果这个链接是链到自己本站的，就在后面添加 token，如果是通向外网则不加。不过，即使这个 csrftoken 不以参数的形式附加在请求之中，黑客的网站也同样可以通过 Referer 来得到这个 token 值以发动 CSRF 攻击。这也是一些用户喜欢手动关闭浏览器 Referer 功能的原因


`4.验证码` 

这个方案的思路是：每次的用户提交都需要用户在表单中填写一个图片上的随机字符串，厄....这个方案可以完全解决CSRF，但个人觉得在易用性方面似乎不是太好，还有听闻是验证码图片的使用涉及了一个被称为MHTML的Bug，可能在某些版本的微软IE中受影响。


**二.什么是XSS跨站脚本攻击？** 

Cross-Site Scripting（跨站脚本攻击）简称 XSS，是一种代码注入攻击。攻击者通过在目标网站上注入恶意脚本，使之在用户的浏览器上运行。利用这些恶意脚本，攻击者可获取用户的敏感信息如 Cookie、SessionID 等，进而危害数据安全。

为了和 CSS 区分，这里把攻击的第一个字母改成了 X，于是叫做 XSS。

XSS 的本质是：恶意代码未经过滤，与网站正常的代码混在一起；浏览器无法分辨哪些脚本是可信的，导致恶意脚本被执行。

而由于直接在用户的终端执行，恶意代码能够直接获取用户的信息，或者利用这些信息冒充用户向网站发起攻击者定义的请求。

在部分情况下，由于输入的限制，注入的恶意脚本比较短。但可以通过引入外部的脚本，并由浏览器执行，来完成比较复杂的攻击策略。


**XSS 分类** 

根据攻击的来源，XSS 攻击可分为存储型、反射型和 DOM 型三种。 

`存储型XSS的攻击步骤：`

攻击者将恶意代码提交到目标网站的数据库中。
用户打开目标网站时，网站服务端将恶意代码从数据库取出，拼接在 HTML 中返回给浏览器。
用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。
这种攻击常见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等。

`反射型XSS的攻击步骤： `

攻击者构造出特殊的 URL，其中包含恶意代码。
用户打开带有恶意代码的 URL 时，网站服务端将恶意代码从 URL 中取出，拼接在 HTML 中返回给浏览器。
用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。
反射型 XSS 跟存储型 XSS 的区别是：存储型 XSS 的恶意代码存在数据库里，反射型 XSS 的恶意代码存在 URL 里。

反射型 XSS 漏洞常见于通过 URL 传递参数的功能，如网站搜索、跳转等。

由于需要用户主动打开恶意的 URL 才能生效，攻击者往往会结合多种手段诱导用户点击。


`DOM型XSS的攻击步骤： `

攻击者构造出特殊的 URL，其中包含恶意代码。
用户打开带有恶意代码的 URL。
用户浏览器接收到响应后解析执行，前端 JavaScript 取出 URL 中的恶意代码并执行。
恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

DOM 型 XSS 跟前两种 XSS 的区别：DOM 型 XSS 攻击中，取出和执行恶意代码由浏览器端完成，属于前端 JavaScript 自身的安全漏洞，而其他两种 XSS 都属于服务端的安全漏洞。

POST的内容也可以触发反射型 XSS，只不过其触发条件比较苛刻（需要构造表单提交页面，并引导用户点击），所以非常少见。


`XSS攻击的预防`

通过前面的介绍可以得知，XSS 攻击有两大要素：攻击者提交恶意代码，浏览器执行恶意代码。

```
利用模板引擎 开启模板引擎自带的 HTML 转义功能。例如： 在 ejs 中，尽量使用` <%= data %> `而不是 `<%- data %>`； 在 doT.js 中，尽量使用 `{{! data }` 而不是` {{= data }`; 在 FreeMarker 中，确保引擎版本高于 2.3.24，并且选择正确的 `freemarker.core.OutputFormat`。
```
避免内联事件 尽量不要使用 `onLoad="onload('{{data}}')"、onClick="go('{{action}}')" `这种拼接内联事件的写法。在 JavaScript 中通过 .addEventlistener() 事件绑定会更安全。

避免拼接 HTML 前端采用拼接 HTML 的方法比较危险，如果框架允许，使用 `createElement、setAttribute `之类的方法实现。或者采用比较成熟的渲染框架，如 Vue/React 等。
时刻保持警惕 在插入位置为 DOM 属性、链接等位置时，要打起精神，严加防范。

增加攻击难度，降低攻击后果 通过 CSP、输入长度配置、接口安全措施等方法，增加攻击的难度，降低攻击的后果。
主动检测和发现 可使用 XSS 攻击字符串和自动扫描工具寻找潜在的 XSS 漏洞。


参考文章：https://tech.meituan.com/2018/09/27/fe-security.html
