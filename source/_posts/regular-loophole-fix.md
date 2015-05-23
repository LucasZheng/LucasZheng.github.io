title: 常规漏洞修复方案
description: 前端开发中常见的漏洞，以及如何修复
date: 2014-12-23 16:57:38

categories: 
- security

tags:
- security

---
总结和收集了一些前后台漏洞修复的方案。<!-- more -->

#### 管理后台对外
Apache 配置文件中修改: `vim /etc/httpd/conf.d/wp.conf`
{% codeblock lang:plain %}
&lt;Directory /var/www/html/wp/wp-admin>
Order Deny,Allow
Deny from all
Allow from 61.135.165
Allow from 220.181.50
Allow from 61.135.169
Allow from 220.181.38
&lt;/Directory>
{% endcodeblock %}

在.../admin/ 目录下设置 .htaccess文件
{% codeblock lang:plain %}
order deny,allow
deny from all
allow from 61.135.165.142/26
allow from 220.181.50.206/26
allow from 61.135.169.68/27
allow from 220.181.38.100/27
allow from 172.0.0.0/8
allow from 10.0.0.0/8
{% endcodeblock %}

Nignx配置
{% codeblock lang:plain %}
location /private/ {
    allow 192.168.1.0/24;
    deny all;
}
location ~ ^/private/.*\.php$ {
    allow 192.168.1.0/24;
    deny all;
    include conf/enable_php5.conf;
}
location ~ \.php$ {
    include conf/enable_php5.conf;
}
{% endcodeblock %}

PHP文件中修改：
{% codeblock lang:plain %}
$laiyuanip=$_SERVER['REMOTE_ADDR'];
if ($ceshijieguo=ereg('^(220.181.50|61.135.165|61.135.169|123.125.64|123.125.113|220.181.38 |10. )', "$laiyuanip") ){
    echo "";
} else {
    echo 'IP 未授权';
    exit;
}
{% endcodeblock %}

#### XSS漏洞
##### 普通XSS
{% codeblock lang:plain %}
1、Htmlspecialchars、htmlentities
$help_array = htmlspecialchars($params['help_array']);
2、str_replace
str_replace(array('<','>'), array('&lt;','&gt;'), $question['title']);
3、如果是smarty ，参考利用Smarty自动转义来避免简单XSS
{% endcodeblock %}

##### json XSS
{% codeblock lang:plain %}
(1) 设置响应类型为 header("Content-Type:text/javascript; charset=UTF-8");
(2) 白名单法：
$score = $_REQUEST['score'];
if(preg_match('/[0-9]{1,2}/$', $score)!==1){
    echo ""; return;
}
{% endcodeblock %}

##### flash xss
{% codeblock lang:plain %}
(1)将ExternalInterface.call参数写死
(2)过滤法：
_loc_1 = this.loaderInfo.parameters;
ExternalInterface.addCallback("capture", this.capture);
_loc_2 = /\W/g;
ExternalInterface.call(_loc_2.test(_loc_1["loadedCallback"]) ? ("a") : (_loc_1["loadedCallback"]));
{% endcodeblock %}

#### Sql注入
{% codeblock lang:plain %}
1、对字符串 mysql_escape_string
$paramStr = mysql_real_escape_string($paramStr);
2、对数字 intval
$param  = intval ($param);
{% endcodeblock %}

#### Nginx解析漏洞
{% codeblock lang:plain %}
（1）php.ini中 cgi.fix_pathinfo = 0
（2）lighttpd增加rewrite规则：
  "\..*/.*\.php" =>"/index.php",
{% endcodeblock %}

#### URL跳转
{% codeblock lang:plain %}
（1）校验refer:
if (strpos($_SERVER['HTTP_REFERER'], 'baidu.com') ==false){
    echo “非法跳转”;
    exit();
}
（2）校验url:
if(strpos($red_url[0],"baidu.com/" ) === false){
    echo “非法跳转”;
    exit();
}
{% endcodeblock %}

#### SVN信息泄露
{% codeblock lang:plain %}
find ./  -type d -name ".svn" |xargs rm –rf {}
{% endcodeblock %}

#### 文件包含
{% codeblock lang:plain %}
(1)过滤法：
$filename = $_GET("filename");
$filter_tags = array('..','%00');
$filename = str_replace($filter_tags,'',$filename);
(2)正则法：
$filename = $_GET['filename'];
if(preg_match("/^[a-zA-Z0-9_-]+$/",$filename) || preg_match("/^[a-zA-Z0-9_-]*\.[a-zA-Z]{3}$/",$filename)) {
} else {
    echo "非法字符";
    exit();
}
{% endcodeblock %}

#### Strusts命令执行
{% codeblock lang:plain %}
1、升级版本
2、在struts.xml中加入了excludeParams从而限制范围
3、过滤法
private String filterOnglString(String url){
    if(url != null){
        return url.replaceAll("%\\{.*\\}", "");
    }else{
        return url;
    }
}
{% endcodeblock %}

#### 列目录
修改Apache配置文件：
{% codeblock lang:plain %}
已经对wm.baidu.com/kr目录进行权限控制
&lt;Directory "/home/work/kr/kr">
Options Indexes FollowSymLinks
AllowOverride None
Order allow,deny
deny from all
&lt;/Directory>
{% endcodeblock %}
或者：
{% codeblock lang:plain %}
&lt;Directory "/home/work/kr/kr ">
Options -Indexes -MultiViews FollowSymLinks
AllowOverride None
Order allow,deny
Allow from all
&lt;/Directory>
{% endcodeblock %}

#### WEB-INF目录被访问
{% codeblock lang:plain %}
&lt;Directory "/home/work/local/apache/htdocs/WEB-INF">
    Deny from all
&lt;/Directory>
{% endcodeblock %}

#### apache httponly cookie泄露
当apache接收到一个大于4k的cookie的时候，会返回一个400的错误，并且把所有的cookie完整的在页面当中显示出来，提示这个cookie过大，那么我们的攻击方式便是利用xss，给用户在正常的cookie的基础上再增加一些无用的cookie，使用户的cookie大于4k，然后js再发包请求一次网站，这时apache返回的响应就是400，并把cookie显示出来，这时我们只要用js正则匹配出正常的cookie即可，由于不是js直接获取cookie，而只是正则匹配出返回的字符串而已，便相当于绕过了cookie的httponly的属性，完整的攻击再加上把匹配的字符串再发送到自己的一个接受文件当中即可。
解决方案：
建议一：升级apache 版本 > 2.2.22

建议二：自定义400错误页面输出一个简单生硬的错误代码：

```ErrorDocument 400 "Bad Request 400"```

#### 版本泄露
{% codeblock lang:plain %}
(1) Nginx
修改nginx配置文件nginx.conf中的server_tokens为off
(2) Apache
修改httpd.conf
ServerSignature Off
ServerTokens ProductOnly
{% endcodeblock %}
