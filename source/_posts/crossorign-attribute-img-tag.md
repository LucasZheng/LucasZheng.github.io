title: img元素中的crossorigin属性 
description: html5中img元素的crossorign属性
date: 2017-04-19 22:52:13
categories:
- html

tags: [crossorign, img]
---
`img`是我们在web开发中比较常用的一个标签，它代表着文档中的一个图像元素。HTML5给这个标签新增加了一个`crossorigin`属性，这个属性决定了图片获取过程中是否开启`CORS`功能<!-- more -->
#### 使用场景
在最近的项目开发中，有一个需求是让用户输入一个任意图片的url地址，在前端预览图片并让用户裁剪，再将裁剪好的图片上传到后端的服务器上。这里输入的图片地址很有可能来自第三方站点，这就涉及到一个跨域的问题，如果仅仅是展示预览图片的话是没有什么问题的，但这里裁剪图片时需要将裁剪好的图片进行base64编码，如果没有使用`crossorign`开启`CORS`功能的话，在使用`canvas`的`toBlob()`、`toDataURL()`和`getImageData()`方法时就会出现跨域的问题。
#### crossorigin属性
这个属性有两个值可选：  
`anonymous`：如果使用这个值的话就会在请求中的`header`中的带上`Origin`属性，但请求不会带上`cookie`和其他的一些认证信息。  
`use-credentials`：这个就同时会在跨域请求中带上`cookie`和其他的一些认证信息。  
在使用这两个值时都需要server端在response的header中带上`Access-Control-Allow-Credentials`属性。  
可以通过server的配置文件来开启这个属性：[server开启Access-Control-Allow-Credentials](https://github.com/h5bp/server-configs-apache/blob/fc379c45f52a09dd41279dbf4e60ae281110a5b0/src/.htaccess#L36-L53)  
前端写法：
```javascript
var img = new Image,
    canvas = document.createElement("canvas"),
    ctx = canvas.getContext("2d"),
    src = "http://example.com/image"; // insert image url here

img.crossOrigin = "anonymous";

img.onload = function() {
    canvas.width = img.width;
    canvas.height = img.height;
    ctx.drawImage( img, 0, 0 );
    localStorage.setItem( "savedImageData", canvas.toDataURL("image/png") );
}
img.src = src;
// make sure the load event fires for cached images too
if ( img.complete || img.complete === undefined ) {
    img.src = "data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///ywAAAAAAQABAAACAUwAOw==";
    img.src = src;
}
```
#### safari浏览器的bug
在safari浏览器中发现一个bug，即用户选择本地的图片文件时，如果使用了`crossorigin`话也会报错，解决办法是判断图片的地址是否是以`http`开头的，即：
```javascript
if (path.substring(0, 4).toLowerCase() === 'http') {
  img.crossOrigin = 'anonymous'
}
```
