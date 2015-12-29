title: RESTful API 
description: RESTful api设计准则及最佳实践
date: 2015-12-29 22:52:13
categories:
- other

tags: [RESTful]
---
RESTful架构是一种流行的互联网软件架构，它结构清晰，符合标准，易于理解，扩展方便。<!-- more -->
REST是Representational State Transfer的缩写，翻译为“表现层状态转化”。表现层其实就是资源，因此可以理解为“资源状态转化”。  
网络应用上的任何实体都可以看作是一种资源，通过一个URI(统一资源定位符)指向它。  

**表现层（Representation）**  
“资源”是一种信息实体，它可以有多种外在表现形式。我们把“资源”具体呈现出来的形式叫做它的“表现层”。
例如，文本可以是txt格式表现，也可以用HTML格式，XML格式，JSON格式表现；图片可以是JPG格式也可以是PNG格式表现等。  
URI之代表资源实体，不代表它的形式。URI应该之代表“资源”的位置，而它具体的表现形式应该在HTTP请求的头信息中用Accept和Content-Type字段指定，这两个字段才是对“表现层”的描述。  

**状态转化（State Transfer）** 
访问一个WEB应用就代表了客户端与服务短的一个互动过程。HTTP协议是一个无状态协议。因此所有的状态都保存在服务端。如果客户端想要操作服务器，就必须通过某种手段让服务端发生“状态变化”（State
Transfer）。而这种状化是建立在表现层之上的，所以就是“表现层状态转化”。  
### RESTful设计准则
+ 统一的接口
+ 无状态
+ 可缓存
+ CS结构
+ 分层系统
+ 按需编码

**统一的接口**  
统一接口定义了客户端和服务端之间通信的接口，它简化和解耦了架构，使接口的每个部分都相对独立，统一接口的四个原则是：
+ 基于每个资源
每个资源都可以用一个URI来表示。资源与它们最终的展现形态在概念上是独立的，比如服务端不会返回一个数据库，而是返回HTML。XML和JSON代表了一些数据记录。
+ 通过表现层修改资源  
每个客户端可以操作（修改或删除等）服务端的资源。
+ 资源的自我定义  
每个资源保护足够的信息来描述自己。如MIME类型，响应内容明确表示它们是否可以被缓存。
+ 资源相关性  
除了传统上请求包含header和response外，REST还意味着可以在响应中包含其他相关资源的api链接。

**无状态**  
无状态是REST的核心。URI只定义一个资源，其他的查询状态可以作为URI的参数来传递，在服务端处理完成后，需要把这些查询状态通过header等方式返回给客户端。  
我们习惯用`session`来维持通信的一些状态。但在REST中，客户端必须给服务端足够的信息来处理这个请求，即使在多个请求时也有必要发送重复的状态信息。无状态特性让服务端维护更加容易，不需要去管session的一些状态。  
那么状态和资源之间有什么区别呢？资源时恒定不变的，它储存在数据库中，不论谁来请求它，都返回相同的内容。状态是变化的，不同的请求带上不同的状态信息，就会得到不同的数据。  

**是否缓存**  
每个响应需要指出是否可以被缓存。  

**CS结构**  
统一的接口让客户端和服务端得以分开，客户端不用考虑数据的存储，服务端不用考虑客户端的状态，它们两者之间可以独立开发。  

**分层系统**  
客户端不知道它是否直接和终端连接，或者是和代理服务器连接。代理服务器可以通过负载均衡和缓存来提升系统的性能和可扩展性。  

**按需编码（可选）**  
服务端可以临时扩展或自定义一些功能，并向客户端发送一些可执行的内容。比如Java  applet或客户端的js脚本。  
总之，除了按需编码外，缺少上面原则中的任何一个在严格意义上都不算是RESTful。  

### RESTful最佳实践
#### 使用RESTful标准的URL和action
每个资源或资源集合都对应着一个URL，URL中应该使用名称来描述，对于操作资源的行为可以使用HTTP请求中的`GET`, `POST`, `PUT`, `PATCH`, `DELETE` 来定义。如下：  
+ GET /tickets    - 获取tickets列表
+ GET /tickets/12   - 获取指定的ticket
+ POST /tickets   - 创建一个新的ticket
+ PUT /tickets/12   - 更新指定ticket
+ PATCH /tickets/12  - 更新指定ticket部分信息
+ DELETE /tickets/12  - 删除指定ticket

在定义URL时，对于资源名称我们应该尽量统一使用复数格式。对于有关联性的资源，RESTful也提供了有用的设计原则，如：  

+ GET /tickets/12/messages    - 获取指定ticket下的message列表
+ GET /tickets/12/messages/5   - 获取指定的ticket下的指定message
+ POST /tickets/12/messages   - 在指定的ticket下创建一个新的message
+ PUT /tickets/12/messages/5   - 在指定的ticket下更新指定message
+ PATCH /tickets/12/messages/5  - 在指定的ticket下更新指定message部分信息
+ DELETE /tickets/12/messages/5  - 在指定的ticket下删除指定message

当然如果资源的关联性没那么强，那么我们也可以将依赖的资源单独独立成一个api，此时我们再请求管理资源时就会多发一个api请求去获取相关资源。

#### 始终使用SSL
你的应用可能会被在任何地方使用，如图书馆，咖啡厅，机场等，这些公共场合都是不安全的，网络通信有可能被劫持。使用SSL的另外一个好处就是你不需要用户的登录来验证它的合法性，而是通过证书来验证。

#### 文档
一个好的api文档要让用户能够快速的找到他所需要的信息，明白各个api的用途。文档应该给出详细的request/response例子。此外如果某些api有更新或不再使用，也需要在文档中给用户明确的提示。  
#### 版本
始终给你的api加上版本号。api的每次更新都应该加上版本号，这样如果我们某些时候需要老版本的api数据，我们就可以请求对应版本的api。版本号可以放在URL中或http的和header中，但通常为了直观明了，将版本号放在URL中。

+ https://example.org/api/v1/*
+ https://api.example.com/v1/*

#### 对资源进行过滤
对资源定义的URL应该尽量简洁，而对资源进行的搜索、过滤、排序等行为可以通过URL的参数来传递。如：

+ GET /tickets?state=open  
+ GET /tickets?sort=-priority
+ GET /tickets?sort=-priority,created_at
+ GET /tickets?state=closed&sort=-updated_at
+ GET /tickets?q=return&state=open&sort=-priority,created_at

#### 指定API返回哪些字段
通常用户只需要api返回中的部分数据，而不是整个资源，此时可以使用`fields`来限制返回的数据。如下： 

+ GET /tickets?fields=id,subject,customer_name,updated_at&state=open&sort=-updated_at

api返回数据的精简同样可以减少网络的请求时间，提升用户的体验。

#### 尽量使用JSON
尽量都适用json来传递数据，如果有使用XML的特殊场景，那么你应该在你的api后面追加`.json`或`.xml`来描述该api返回什么格式的数据。
#### 使用驼峰形式给字段命名
如果你使用JSON，那么就应该使用javascript的命名规范：使用驼峰形式给字段命名。如果你使用的其他语言，则最好遵循它们自己的规范:c#和java采用驼峰形式，python和rugy采用下划线形式。  
#### API返回数据的可读性
api返回的数据可以展开以可读的方式呈现给用户，也可以压缩成一行数据传递。压缩后的数据确实能带来更少的网络负载，但如果开启了gzip功能后，api的压缩和未压缩前并未带来明显的效果，而未压缩的api能够提升它的可读性，因此尽量避免对api返回的数据进行压缩。  
#### 不要采用信封的方式来封装数据
尽量不要采用如下的格式来封装数据： 
```json
{
  "data" : {
    "id" : 123,
    "name" : "John"
  }
}
```

除非在使用`jsonp`来处理跨域请求时（通常url中带有`callback`或`jsonp`），这时可以采用如下的格式来封装数据：

```json
callback_function({
  status_code: 200,
  next_page: "https://..",
  response: {
    ... actual JSON response body ... 
  }
})
```

#### 错误信息
请求api时返回错误信息有时是不可避免的，在返回的信息中我们应该尽量包含完整的说明，如下： 
```json
{
  "code" : 1234,
  "message" : "Something bad happened :(",
  "description" :1234 "More details about the error here"
}
```

对应`PUT`，`PATCH`，`POST`我们还需要给出更详细的错误信息，如：  
```json
{
  "code" : 1024,
  "message" : "Validation Failed",
          "errors" : [
    {
      "code" : 5432,
      "field" : "fieldsrst_name",
      "message" : "First name cannot have fancy characters"
    },
    {
       "code" : 5622,
       "field" : "password",
       "message" : "Password cannot be blank"
    }
  ]
}
```

#### HTTP状态码
HTTP中定义了许多状态码，api返回的状态码应该遵循既有的原则，让用户能够一目了然的明白当前的请求状态。常用状态码如下：

+ 200 - OK
+ 304 - Not Modified
+ 400 -Not Bad Request
+ 401 - Unauthorized
+ 403 - Forbidden
+ 404 - Not Found
+ 500 - Internal server error
  
#### 其他
+ API的身份认证应该使用OAuth2.0框架
