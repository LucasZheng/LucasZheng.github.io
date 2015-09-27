title: 初探单页应用的安全性
description: 如何防范单页应用中的漏洞攻击
date: 2015-09-27 16:21:55
categories:
- security

tags: [security]
---
现在的SPAs(Single Page Applications)和移动端webapp正面临新的安全威胁。我们称这些应用为‘不信任的客户端’，因为我们的服务端代码不能控制运行环境的安全性。<!-- more -->即使是常规的web应用也面临着这些问题。攻击者很容易的在页面上的console平台上注入javascript代码。像android和ios上的app很容易被反编译和攻击。因此我们在这些app中就不能明文传输一些重要的机密信息。
这篇文章将会分析常见的安全攻击及采取如何应对措施。
#### 当前Webapps的安全考虑
防止web应用被攻击的首要任务就是阻止可疑的代码运行，我们要以一种更安全的方式来传送重要数据，保护我们的API接口，同时设置用户接入应用的规则。
##### XSS(Cross Site Scripting)攻击
XSS攻击就是你的应用被注入了javascript代码并得到运行，它经常发生在页面的表单上。
下面这个图就能很好的解释XSS是如何攻击页面的：
![XSS](/images/xss.gif)
由于这个页面没有对XSS攻击采取防范措施，因此在输入框中输入如下脚步：
```javascript
xss<script>alert('hi!');</script>
```
当点击搜索时，代码就会被执行。
这里有一个更详细的文档来介绍如何防范XSS攻击：[cheat sheet](https://www.owasp.org/index.php/XSS_%28Cross_Site_Scripting%29_Prevention_Cheat_Sheet)。
但总的来说，防止XSS有效的办法就是对输入的内容都进行转义。
##### 安全的用户证书
传统上，用户通过填写表单上的用户名和密码然后用HTTP的POST方法将数据安全的传送到服务器端。假设用户的信息是正确的，服务器端产生一个唯一的session id并且通过响应的header中的Set-Cookie来发送到客户端。接下来的请求中都会带上这个id，如下图：
![session_cookie](/images/session_cookie.png)
实用session id有如下好处：
+ 不必在每次请求中都带上用户名和密码，session id就可以用户代表当前用户
+ 服务端可以用session id来存储用户相关的信息，比如用户名、权限及meta信息

##### 正确的实用cookie
Cookie如果使用得当得话是非常有用的，但是它也不是万能的，下面我们将看到它可能存在的两个漏洞：
###### 中间劫持
中间劫持是你认为你正连接到某个特定的服务器端，但实际上你却连接在一个监听者的服务器上。监听的服务器能够在你的客户端和实际服务器间转发监听信息。
有一个场景，当你从使用HTTPS切换到HTTP时，这时就不能保证安全性，一旦连接降级，session id将会网络中明文传输，任何监听者都可以使用该session id。这就是一个典型的中间劫持，目的是获取你的session id然后使用该id来访问你那些需要授权的站点，这也称为session劫持。
补救的方法就是都统一使用HTTPS通信，并且使用TLS即使是在内网中。
##### 跨站点请求伪造(CSRF)
CSRF可以用如下场景来描述：
+ 你登陆进你的银行账号并查看余额和交易信息
+ 你并没有注销登陆的账号
+ 你获取到一封邮件，其中有一个吸引你的链接
+ 出于好奇，你点击这个链接。但其实这个链接的地址是你的银行站点，并且是发送一笔交易给攻击者

这是如何发生的呢？首先，当然真实场景中是不太可能发生这种情况，因为银行一般都会对CSRF攻击有防范措施。攻击者事先不知道你的session id，只能假设你正登陆进银行站点，并且没有退出，此时如果你点击链接到你的银行，那么默认就会带上之前生成的session id，而且进行接下来的交易操作，链接代码如下：
```html
<a href="https://myficticiousbank.com/transfer?to=MrBadGuy&amount=10000">
    See Cute Cats!
</a>
```
当然可以采用如下三种方法来防范CSRF：
+ 同步Token信息
+ 双重提交认证
+ 检测header中的Origin

###### 同步Token信息
采用同步Token的方法，就是让服务端产生一个隐藏的变量放在表单中，当表单提交时，服务端检测表单是否携带正确的变量数据，如下图：
![synchronizer_token_success](/images/synchronizer_token_success.png)
现在，假设你收到一封邮件，且邮件中有一个吸引你的链接，但这个链接实际上是攻击者想让你进行某些操作，从而达到对方的目的。这个链接表面上看上去没什么两样，但是，你的站点采用了同步Token的措施来防范CSRF，因此，当你点击链接时，链接中想要达到真实的操作目的就会失败，因为攻击者不知道真实的Token数据是什么，他也就达不到攻击目的。
![synchronizer_token_failure](/images/synchronizer_token_failure.png)
当然这种Token数据我们最好是每次都进行更新，尽量不要重复使用。
###### 双重提交认证
采用双重提交认证，就是发送两个Cookie到客户端。第一个是session id，而第二次是一个随机数据。这种有效的方法取决于两种因素：一是浏览器的同源策略，只允许同源的请求得到处理；第二是再次发送的请求都需要带上随机数据。如下图：
![double_submit_cookie](/images/double_submit_cookie.png)
当你登陆页面时，服务端会发送两个Cookie数据，第二个使用了自定义的header（如当前使用的X-XSRF-Token）,再次请求时，服务器会检测自定义的header是否正确。当然攻击者不能获取到自定义的header数据，也就不能实施有效的攻击。
###### Origin检测
所有的浏览器，包括IE9+，在请求中都会带上一个`Origin`信息，这个值是不允许被javascript修改的，保证了这个值的真实性。服务器能够检测Origin中的url是否跟期望匹配。攻击者发送请求中的Origin一般都满足服务器的期望，如下：
![origin_header](/images/origin_header.png)
当我提交一个表单来注册时，浏览器就会自动带上一个`Origin: https://api.stormpath.com`的信息，Stormpath服务器就会拒绝非这个origin的请求。
##### Session id 所面临的问题
如果你的用户数量很庞大，需要多台服务器来实现负载均衡时，当一台服务器挂掉后，那么用户的之前请求就会被其他服务器来接管，这时如何在多台服务器之间来共享Session id呢？当然我们需要一个服务来存储Sesson id，并且每个服务器都能共享这个值，往往这个值一般是存储在数据库或硬盘上，然而用户的每次请求都会去读取这个值，这必然给服务器带来一定的压力。



