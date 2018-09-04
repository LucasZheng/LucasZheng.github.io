title: 将footer固定在页面底部的实现方法
description: 将footer固定在页面底部的实现方法
date: 2015-04-23 22:54:13
categories:
- html

tags: [html]
---
在前端开发中，需要将共用的footer组件始终置于页面的底部，下面介绍几种实现方法<!-- more -->
#### 方法一：footer高度固定+绝对定位
HTML结构：

```html
<body>
  <header>header</header>
  <main>main content</main>
  <footer>footer</footer>
</body>
```

css设置：

```css
html{height:100%;}
body{min-height:100%;margin:0;padding:0;position:relative;}

header{background-color: #ffe4c4;}
main{padding-bottom:100px;background-color: #bdb76b;}/* main的padding-bottom值要等于或大于footer的height值 */
footer{position:absolute;bottom:0;width:100%;height:100px;background-color: #ffc0cb;}
```

#### 方法二：footer高度固定+margin负值
HTML结构：

```html
<body>
  <div class="container">
    <header>header</header>
    <main>main content</main>
  </div>
  <footer>footer</footer>
</body>
```

css设置：

```css
html,body{height:100%;margin:0;padding:0;}

.container{min-height:100%;}
header{background-color: #ffe4c4;}
main{padding-bottom:100px;background-color: #bdb76b;}/* main的padding-bottom值要等于或大于footer的height值 */
footer{height:100px;margin-top:-100px;background-color: #ffc0cb;}/* margin-top（负值的）高度等于footer的height*/
```

#### 方法三：footer高度任意+js
HTML结构：

```html
<body>
  <header>header</header>
  <main>main content</main>
  <footer>footer</footer>
</body>
```

css设置：

```css
html,body{margin:0;padding: 0;}
header{background-color: #ffe4c4;}
main{background-color: #bdb76b;}
footer{background-color: #ffc0cb;}

/* 动态为footer添加类fixed-bottom */
.fixed-bottom {position: fixed;bottom: 0;width:100%;}
```

js代码：

```javascript
$(function(){
  function footerPosition(){
    $("footer").removeClass("fixed-bottom");
    var contentHeight = document.body.scrollHeight,//网页正文全文高度
      winHeight = window.innerHeight;//可视窗口高度，不包括浏览器顶部工具栏
    if(!(contentHeight > winHeight)){
      //当网页正文高度小于可视窗口高度时，为footer添加类fixed-bottom
      $("footer").addClass("fixed-bottom");
    }
  }
  footerPosition();
  $(window).resize(footerPosition);
});
```
