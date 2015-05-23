title: PHP中获取当前页面的完整URL
description: 如何通过php快速获取页面URL中传递的数据
date: 2014-12-28 20:30:30
categories:
- php

tags: [php]
---
在后端业务逻辑处理中，我们往往需要获取页面所发送的url参数，如下通过$_SERVER可以获取到url中的所有内容：<!-- more -->
#### php实现
测试网址:     
http://localhost/blog/testurl.php?id=5
{% codeblock lang:php %}
//获取域名或主机地址 
echo $_SERVER['HTTP_HOST']; #localhost

//获取网页地址 
echo $_SERVER['PHP_SELF']; #/blog/testurl.php

//获取网址参数 
echo $_SERVER["QUERY_STRING"]; #id=5

//获取用户代理 
echo $_SERVER['HTTP_REFERER']; 

//获取完整的url
echo 'http://'.$_SERVER['HTTP_HOST'].$_SERVER['REQUEST_URI'];
echo 'http://'.$_SERVER['HTTP_HOST'].$_SERVER['PHP_SELF'].'?'.$_SERVER['QUERY_STRING'];
#http://localhost/blog/testurl.php?id=5

//包含端口号的完整url
echo 'http://'.$_SERVER['SERVER_NAME'].':'.$_SERVER["SERVER_PORT"].$_SERVER["REQUEST_URI"]; 
#http://localhost:80/blog/testurl.php?id=5

//只取路径
$url='http://'.$_SERVER['SERVER_NAME'].$_SERVER["REQUEST_URI"]; 
echo dirname($url);
#http://localhost/blog
{% endcodeblock %}
