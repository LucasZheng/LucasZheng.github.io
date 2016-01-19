title: SVG的用法 
description: 全面解析SVG的各种用法
date: 2016-01-20 22:52:13
categories:
- css

tags: [SVG]
---
SVG是一种矢量图格式。SVG是Scalable Vector Graphics三个单词的首字母缩写。Adobe Illustrator是专门编辑、制作矢量图的软件工具。SVG是Adobe Illustrator的主要制作目标。你可以在轻松的网页上使用漂亮的SVG图，但SVG有很多的用法你可能还不知道。<!-- more -->

#### SVG的优势
+ 文件体积小，能够被大量的压缩
+ 图片可无限放大而不失真(矢量图的基本特征)
+ 在retina屏幕上显示效果极佳
+ 能够实现互动和滤镜效果

#### SVG的制作方法
用Adobe Illustrator设计制作。下面是一张新西兰鹬鸵鸟的图片。
![](/images/kiwi.png)
Adobe Illustrator能直接把文件保存成SVG格式。
![](/images/save-as-svg.png)

#### SVG图片的用法
如果你把SVG保存成文件，你可以直接把它放到<img>标记里。
```html
<img src="kiwi.svg" alt="新西兰鹬鸵鸟">
```

当然，你可以像PNG和JPG图片那样通过`width`和`height`改变它的大小。这种用法除了在IE8及以下和Android2.3及以下都是ok的。
如果你想要在所有浏览器中都支持SVG图片，你可以用Modernizr测试浏览器的支持情况，必要时以普通图片代替：
```js
if (!Modernizr.svg) {
  $(".logo img").attr("src", "images/logo.png");
}
```
或者：
```html
<img src="image.svg" onerror="this.onerror=null; this.src='image.png'">
```

#### SVG背景图的用法
跟SVG图片用法相似，你也可以在背景图(`background-image`)中使用SVG。
```html
<a href="/" class="logo">
  新西兰鹬鸵鸟
</a>
```
css:
```css
.logo {
  display: block;
  text-indent: -9999px;
  width: 100px;
  height: 82px;
  background: url(kiwi.svg);
  background-size: 100px 82px;
}
```

`background-size`的长宽比例要和原图一致。但你可以使用`background-size`的常量值contain或cover来达到不同的效果。
同样，这中用法除了在IE8及以下和Android2.3及以下都是ok的。
Modernizr在这里也能起到作用，而且更高效。如果浏览器不支持SVG背景图时，SVG文件将不会被加载，因为Modernizr在检测到不支持SVG时，在HTML上加了"no-svg"CSS类，于是我们可以这样利用它：
```css
.main-header {
  background: url(logo.svg) no-repeat top left;
  background-size: contain;
}

.no-svg .main-header {
  background-image: url(logo.png);
}
```

另外一个更巧妙的方法是使用多背景图技术。因为各浏览器对SVG和对多背景图技术的支持情况很相似，如果支持多背景图，也会支持SVG，所以，我们的CSS声明可以这样写：
```css
body {
  background: url(fallback.png);
  background-image: url(image.svg), none;
}
```

#### "inline" SVG的用法
你实际上可以把SVG代码从SVG文件里拷贝出来，直接粘贴掉HTML里使用，它们跟你直接使用普通图片的效果一样。
```html
<body>

   <!-- paste in SVG code, image shows up!  -->

</body>
```

这样做的好处是图片数据就在HTML页面里，不需要浏览器单独执行一次请求下载它。换句话说，它跟使用Data URI的优势一样。但不好的地方也是一样。比如“臃肿”的页面代码，缓存问题等。
你应该不会吃惊：用Adobe Illustrator保存的SVG文件并不是优化过的。里面有DOCTYPE、生成工具署名等无用的信息。SVG文件本身很小，但谁会嫌弃更小？Peter Collingridge有一个在线的[SVG优化](http://petercollingridge.appspot.com/svg_optimiser)工具。传上去大的，下载出小的。
你有没有感觉SVG代码跟HTML代码很相似？这是因为它们本质上都是XML。在我们的这个SVG文件中有两个元素，一个`<ellipse>` 和一个`<path>`。我们可以直接在SVG代码添加CSS类信息，就像跟编写HTML代码一样。
```html
<svg ...>
  <ellipse class="ground" .../>
  <path class="kiwi" .../>
</svg>
```

现在，页面中的CSS样式对SVG里的元素同样有效。你不需要在SVG代码直接嵌入CSS代码，放在页面任何一个地方都可以，甚至可以放到单独的CSS文件里，使用`<link>`标记加载到页面上。你甚至可以对SVG元素使用:hover语法。但请注意，有个别的CSS属性对SVG是不起作用的。例如，`background-color`对SVG就是无效的，因为SVG里使用`fill`属性。

```css
.kiwi {
  fill: #94d31b; 
}
.kiwi:hover {
  fill: #ace63c; 
}
```

更绝妙的是，SVG里可以实现所有的滤镜效果。例如模糊处理。我们来试一下：

```html
<svg ...>
  ...
  <filter id="pictureFilter" >
    <feGaussianBlur stdDeviation="5" />
  </filter> 
</svg>
```

现在你可以对它运用任何的CSS样式：

```css
.ground:hover {
  filter: url(#pictureFilter);
}
```

但基本来说和SVG图片一样。除了在IE8及以下和Android2.3及以下都是ok的。
一种对不支持inline SVG的浏览器的补救方法：
```html
<svg> ... </svg>
<div class="fallback"></div>
```
仍然使用Modernizr：
```css
.fallback { 
  display: none;
  /* 确保尺寸是一样大小 */
}
.no-svg .fallback { 
  background-image: url(logo.png); 
}
```

#### SVG对象(object)的用法
如果"inline" SVG不合你的胃口(它有自己的弊端，如缓存问题)，你可以使用HTML的`<object>`标记引用它，这样仍能在浏览器不支持的情况下用CSS进行弥补。
```html
<object type="image/svg+xml" data="kiwi.svg" class="logo">
  新西兰鹬鸵鸟 <!-- 图片备份支持 -->
</object>
```
使用Modernizr检测浏览器的支持情况，用CSS进行挽救：
```css
.no-svg .logo {
  width: 200px;
  height: 164px;
  background-image: url(kiwi.png);
}
```

这种方法能够很好的利用浏览器的缓存。但是，如果你想让CSS样式也起作用，你不能把这些样式放在外部的CSS样式文件里或防止在页面中的`<style>`标记内，你需要把`<style>`信息放在SVG文件内部。
```html
<svg ...>
  <style>
    /* 把你的CSS放到SVG文件内部 */
  </style>
  ...
</svg>
```

#### SVG Data URI的用法
html的用法：
```html
<img src="data:image/svg+xml;base64,[data]>
```
css的用法：
```css
.logo {
  background: url(data:image/svg+xml;base64,[data]);
}
```
object的用法：
```html
<object type="image/svg+xml" data="data:image/svg+xml;base64,[data]>
  fallback
</object>
```


