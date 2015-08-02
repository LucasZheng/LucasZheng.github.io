title: window.fetch的使用
description: window中fetch api使用详解
date: 2015-08-03 23:27:46

categories: 
- javascript

tags: [javascript, api]

---
在前端开发中AJAX是我们常见的异步获取数据的方式，`XMLHttpRequest`当然对我们来说也不陌生。而现在我们有一种相对应XHR更加优雅的API可以使用，那就是`fetch` API。 <!-- more --> `window.fetch` 现在只在firefox 和最新的chrome中得到支持。

#### XMLHttpRequest
在我的印象中`XHR`的创建有点复杂，经常你会像下面这种方式来写：
``` javascript
// Just getting XHR is a mess!
if (window.XMLHttpRequest) { // Mozilla, Safari, ...
  request = new XMLHttpRequest();
} else if (window.ActiveXObject) { // IE
  try {
    request = new ActiveXObject('Msxml2.XMLHTTP');
  } 
  catch (e) {
    try {
      request = new ActiveXObject('Microsoft.XMLHTTP');
    } 
    catch (e) {}
  }
}

// Open, send.
request.open('GET', 'http://davidwalsh.name/ajax-endpoint', true);
request.send(null);
```
当然我们所使用的一些框架可以让你使用更简洁的代码来实现，但是上面创建`XHR` 也未免有点复杂化了。
#### `fetch`的使用
现在`fetch`是作为`window`下的一个function，其中第一个参数是URL。
```` javascript
// url (required), options (optional)
fetch('/some/url', {
  method: 'get'
}).then(function(response) {
  
}).catch(function(err) {
  // Error :(
});
```

`fetch`采用了`promise`的方式来处理返回结果或回调函数：
``` javascript
// Simple response handling
fetch('/some/url').then(function(response) {
  
}).catch(function(err) {
  // Error :(
});

// Chaining for more "advanced" handling
fetch('/some/url').then(function(response) {
  return //...
}).then(function(returnedValue) {
  // ...
}).catch(function(err) {
  // Error :(
});
```

如果你还没有使用过`then`这种代码，那么应该开始学会使用了，不久你就会看见它无处不在。
#### 处理JSON数据
假如你要请求一个json数据，那么返回的回调数据就会有一个`json`方法可以将原始数据转换为javascript对象：
``` javascript
fetch('http://davidwalsh.name/demo/arsenal.json').then(function(response) { 
  // Convert to JSON
  return response.json();
}).then(function(j) {
  // Yay, `j` is a JavaScript object
  console.log(j); 
});
```

当然这是使用了`JSON.parse(jsonString)`方法，但是`json`确实是一个很简洁的缩写方式。
#### 处理Text/HTML响应
JSON并不是我们每次请求想要得到的格式，你还可以请求HTML或text响应：
``` javascript
fetch('/next/page')
  .then(function(response) {
    return response.text();
  }).then(function(text) { 
    console.log(text); 
  });
```

你可以通过Promise的`then`链式调用方法来处理text数据。
#### Headers和Metadata
响应的headers和metadata可以通过`response`对象获取到，headers可以通过`get`方法获取：
``` javascript
fetch('http://davidwalsh.name/demo/arsenal.json').then(function(response) {
  // Basic response properties, available at any time
  console.log(response.status)
  console.log(response.statusText)

  // Grabbing response headers via `get`
  console.log(response.headers.get('Content-Type'))
  console.log(response.headers.get('Date'))
})
```

同样你也可以通过`set`来初始化请求的headers:
``` javascript
// url (required), options (optional)
fetch('/some/url', {
  headers: {
    'Accept': 'application/json',
        'Content-Type': 'application/json'
  }
});
```

#### 处理表单数据
另外一个常用AJAX的场景是处理表单数据：
``` javascript
fetch('/submit', {
  method: 'post',
  body: new FormData(document.getElementById('comment-form'))
});
```

如果你想post json数据到server：
``` javascript
fetch('/submit-json', {
  method: 'post',
  body: JSON.stringify({
    email: document.getElementById('email')
    answer: document.getElementById('answer')
  })
});
```

这些都比较容易实现。
#### 总结
`fetch` api确实比较好使用，但目前它不支持取消一个请求，这让一些开发者在选择使用它时有些犹豫。  
但总的来说，`fetch`比XHR使用上更加简单，我们也期望有更多的浏览器来支持这个方法。如果你想更深入的了解这个api，可以参考：[Introduction to Fetch](http://updates.html5rocks.com/2015/03/introduction-to-fetch)，当然Github上也有对应的polyfill：[Github's implementation](https://github.com/github/fetch)。
