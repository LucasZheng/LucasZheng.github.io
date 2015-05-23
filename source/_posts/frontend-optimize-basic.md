title: 前端性能优化之基础
description: 前端开发中常用的性能优化措施
date: 2014-12-25 21:44:10

categories: 
- performance

tags:
- performance

---
如今浏览器能够实现的特性越来越多，并且网络逐渐向移动设备转移，使我们的前端代码更加紧凑，如何优化，就变得越来越重要了。<!-- more -->开发人员普遍会将他们的代码习惯优先于用户体验。但是很多很小的改变可以让用户体验有个飞跃提升，所以任何一点儿小小的优化都会提升你网站的性能。
#### 减少HTTP请求
##### 图片地图
{% codeblock lang:html %}
<img src="planets.jpg" border="0" usemap="#planetmap" alt="Planets" />
<map name="planetmap" id="planetmap">
    <area shape="circle" coords="180,139,14" href ="venus.html" alt="Venus" />
    <area shape="circle" coords="129,161,10" href ="mercur.html" alt="Mercury" />
    <area shape="rect" coords="0,0,110,260" href ="sun.html" alt="Sun" />
</map>
{% endcodeblock %} 

不是很常用，设置图片的不同区域热点，跳转到对应的url。难点在于不好精确控制坐标，比较适合与图片规整的导航栏。   
##### css sprites

常用方案，集合小的图片或icon于一张图片，通过位置来显示对应的元素
##### 内联图片
常用方案，在css文件中常用图片base64方案，ie6和ie7不支持图片base64。
##### 合并脚本和样式表
必须掌握的技能，不多说。
#### CDN
将页面所使用的静态组件放在CDN上，让不同位置的用户就近获取资源
#### 添加Expires头
添加Expires头，通过浏览器缓存减少HTTP请求数量，并减小HTTP响应的大小

适用于那些不经常更新或变化的资源，通过Expires设置一个过期时间告诉web客户端它可以使用一个组件的当前副本，知道指定的时间为止。

在HTTP1.1中引入了Cache-Control头来客服Expires头的限制。因为Expires头使用一个特定的时间，它要求服务器和客服端的时钟严格同步。另外过期时间需要经常检查，并且一旦未来这一天时间到来，还需要在服务器配置中设置一个新的日期。

Cache-Control使用max-age指令指定组件被缓存多久。它以秒为单位，如果从组件被请求开始过去的秒数少于max-age，浏览器就使用缓存的版本。如：Cache-Control：max-age=132334324。不支持HTTP1.1的浏览器（现在几乎很少）不能使用Cache-Control max-age。可以同时指定两个响应头：Expires和Cahce-Control。如果两者同时出现，HTTP规范规定max-age指令将重写Expires头。

在Apache模块中可以如下设置：
{% codeblock lang:plain %}
&lt;FilesMatch "\.(gif|jpg|js|css)$">
ExpiresDefault "access plus 10 years"
&lt;/FilesMatch>
{% endcodeblock %} 

它同时向响应中发送Expires头和Cache-Control max-age头。
#### 压缩组件 
从HTTP1.1开始，web客户端可以通过HTTP请求中的Accept-Encoding头来标识对压缩的支持。
{% codeblock lang:plain %}
Accept-Encoding：gzip
{% endcodeblock %} 

如果web服务器看到请求中的这个头，就会使用客户端列出来的方法中的一种来压缩响应。web服务器通过响应中的Content-Encoding头来通知web客户端。（gzip是目前最流行和有效的压缩方法）
{% codeblock lang:plain %}
Content-Encoding：gzip
{% endcodeblock %} 

压缩的成本有：服务器端会花额外的cpu来完成压缩，客户端要对压缩文件进行解压缩。需要权衡响应大小和服务端的开销。根据经验，通常对大于1KB或2KB的文件进行压缩（图片、pdf等不必压缩），通常压缩html文件，脚本和样式文件。通过压缩通常能将响应的数据量减少将近70%。

**配置**

Apache1.3的gzip压缩由modgzip模块提供，而Apace2.x中的压缩通过moddeflate模块来完成。具体配置可以网上查询

**代理缓存**

多个客服端使用同一个代理时，若不同客户端中有的支持gzip压缩，有的不支持，而因为代理会对服务器响应的同一个文件进行缓存，因此若第一个请求不支持gzip压缩时，代理缓存未压缩的文件，当第二个支持gzip压缩的请求到达代理时，代理会返回未压缩的文件，失去了进行压缩几的机会，反过来，第一个支持gzip压缩的请求到达代理时，代理缓存压缩过的文件，不支持压缩的请求到达代理时，代理返回压缩过的文件，这时情况就会更严重了。解决方法是在web服务器的响应中添加Vary头。web服务器告诉代理根据一个或多个请求头来改变缓存的响应。
{% codeblock lang:plain %}
Vary：Accecpt-Encoding
{% endcodeblock %}

这使得代理响应缓存的多个版本，为Accecpt-Encoding请求头的每个值缓存一份。  
#### 将样式表放在顶部  
使用link标签将样式表放在文档HEAD中，先加载样式表完成后，再构建dom树结构，页面可以逐步呈现浏览样式，减少白屏的现象。如果样式表放在底部，dom结构下载完成在下载样式表时，样式表下载完成时会对dom进行重绘，出现“无样式内容的闪烁”。
#### 将脚本放在底部
对响应时间影响最大的是页面中组件的数量。当缓存为空时，每个组件都会产生一个HTTP请求。

HTTP1.1规范建议浏览器从每个主机名同时并行地下载两个组件。但今天各个浏览器有自己的并行下载个数。将组件分散在不同的主机上，可以减少响应的时间，但增加并行下载数量并不是没有开销，其优劣取决于你的带宽和cpu速度，过多的并行下载反而会降低性能。
#### 避免css表达式
表达式可以解决ie6不能识别：min-width或max-width:
{% codeblock lang:css %}
width:expression(document.body.clientWidth < 600 ? '600px' : 'auto');
min-width: 600px;
{% endcodeblock %}

#### 使用外部javascript和css
外部文件javascript或css有机会被浏览器缓存起来。
主页加载完成后（onload）动态加载其他页面的静态组件

#### 减少DNS查找

DNS可以被缓存起来提供性能。

#### 精减javascript

压缩混淆js文件，减少js和css中重用的组件。

#### 避免重定向
重定向会使页面变慢。当web服务器向浏览器返回一个重定向时，响应中就会拥有一个范围在3xx的状态码。这表示用户代理必须执行进一步操作才能完成请求。304：not modified；301：moved permanently。

通过meta标签重定向：
{% codeblock lang:html %}
<meta http-equiv="refresh" content="0"; url="xxx">
{% endcodeblock %}

访问的目录url后面加"/"，减少重定向

#### 移除重复脚本

#### 配置etag

etag是web服务器和浏览器用于确认缓存组件的有效性的一种机制。

服务器在检测缓存的组件是否和原始服务器上的组件匹配时有两种方式：
* 比较最新修改日期
* 比较实体标签

etag是唯一标识了一个组件的一个特定版本的字符串。唯一的格式约束是该字符串必须用引号引起来。

etag带来的问题：对应寄宿与多个不同服务器上的相同组件，它们的etag可能是不一样的。且If-None-Match比If-Modified-Since具有更高的优先级。建议移除etag，可以减少响应和后续请求的HTTP头的大小。

#### 使用ajax缓存

改善ajax请求的最重要的方式是使响应可缓存。
{% codeblock lang:plain %}
Cache-Control：no-store
{% endcodeblock %}
