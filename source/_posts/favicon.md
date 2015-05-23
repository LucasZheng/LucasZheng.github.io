title: favicon的使用
description: favicon的制作以及如何在html中引入
date: 2015-01-01 22:52:13
categories:
- other

tags: [favicon]
---
在浏览网页的时候，我们会发现在浏览器地址栏的左侧会有一个小的icon图标，它就是所谓的icon，用来作为网页的一个标识。在建站的时候，建议增加favicon来提升用户的体验。<!-- more --> 下面谈谈favicon的用法：
**favicon的格式大小**
一般为16x16或32x32像素大小，格式可以为png、gif或ico
**使用方式**
* 将图标放置在网站的根目录下，该网站的每个网页请求都会读取并应用该icon。但要注意某些站点后端一般对所有请求都做了重定向，可能会在请求favicon时出现一些warning的日志。这时最好按照下面的方式来应用。
* 在head标签中加入下面的代码：
{% codeblock lang:html %}
<link rel="shortcut icon" href="http://example.com/favicon.ico" type="image/vnd.microsoft.icon"> 
<link rel="icon" href="http://example.com/favicon.ico" type="image/vnd.microsoft.icon">
{% endcodeblock %}

然而，只有第一行是必须的，因为“shortcut icon”字符串将被多数遵守标准的浏览器识别为列出可能的关键词，而Internet Explorer将会把它作为一个单独的名称（“shortcut icon”）。这样做的结果是所有浏览器都可以理解此代码。只有当希望为新浏览器提供另一种备用图像（例如动画GIF）时，才有必要添加第二行。这样可以针对每个页面有自己的icon，比较灵活。设置服务器，以发送正确的MIME标识：ICO 文件 image/vnd.microsoft.icon；GIF 文件 image/gif；PNG 文件 image/png。

**favicon的在线制作网址**
<http://www.favicon.cc/>
<http://www.xiconeditor.com/>
