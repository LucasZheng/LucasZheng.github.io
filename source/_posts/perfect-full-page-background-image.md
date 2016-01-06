title: 背景图案占满整个屏幕的完美解决方案 
description: 背景图片始终占满整个屏幕的解决办法
date: 2016-01-07 22:52:13
categories:
- css

tags: [image]
---
这篇文章主要是讨论一种让背景图案始终布满整个浏览器的解决方案。主要有以下几点内容：<!-- more -->
+ 让图片占满整个页面
+ 图片可以根据实际情况自动缩放
+ 保持图片原有的宽高比例
+ 避免呈现滚动条
+ 尽可能的实现浏览器兼容
+ 实现类似Flash般的效果

![](/images/fullpagebackground.jpg)

#### 简单有效的CSS3实现方式
我们可以用css3的`background-size`属性来实现，但我们更倾向于将这个属性应用在`html`而不是`body`标签上，因为`html`的高度至少是和浏览器的高度一致。
```css
html { 
  background: url(images/bg.jpg) no-repeat center center fixed; 
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  background-size: cover;
}
```
这种实现可以在如下浏览器中有效：
+ Safari 3+
+ Chrome Whatever+
+ IE 9+
+ Opera 10+(Opera 9.5支持`background-size`属性，但不支持`cover`值)
+ Firefox 3.6+

如果我们想让IE低版本也实现这种效果的话，可以采用如下写法：
```css
filter: progid:DXImageTransform.Microsoft.AlphaImageLoader(src='.myBackground.jpg', sizingMethod='scale');
-ms-filter: "progid:DXImageTransform.Microsoft.AlphaImageLoader(src='myBackground.jpg', sizingMethod='scale')";
```
#### 只用css的方式之一
此外，我们可以用`<img>`标签来实现。我们设置`min-height`来保持垂直方向上始终占满浏览器，在水平方向上设置`width`为100%，同时我们设置`min-width`来保证浏览器缩小到一定程度时，图片不会再缩小。当浏览器缩小到比图片还小时，我们可以设置`left`和`margin-left`来让图片居中显示。
```css
img.bg {
  /* Set rules to fill background */
  min-height: 100%;
  min-width: 1024px;

  /* Set up proportionate scaling   */
  width: 100%;
  height: auto;

  /* Set up positioning */
  positioningon: fixed;
  top: 0;
  left: 0;
}

@media screen and (max-width: 1024px) { /* Specific to this particular
image */
  img.bg {
    left: 50%;
    margin-left: -512px;   /* 50% */
  }
}
```

#### 只用css的方式之二
```html
<img src="images/bg.jpg" id="bg" alt="">
```
css:
```css
#bg {
  position: fixed; 
  top: 0; 
  left: 0; 

  /* Preserve aspet ratio */
  min-width: 100%;
  min-height: 100%;
}
```
但是，这种方式并不能保证图片居中，所以我们可以在它的外面包裹一个div。
```html
<div id="bg">
  <img src="images/bg.jpg" alt="">
</div>
```
css:
```css
#bg {
  position: fixed; 
  top: -50%; 
  left: -50%; 
  width: 200%; 
  height: 200%;
}
#bg img {
  position: absolute; 
  top: 0; 
  left: 0; 
  right: 0; 
  bottom: 0; 
  margin: auto; 
  min-width: 50%;
  min-height: 50%;
}
```

#### jQuery方式
通过jQuery来获取图片的高宽比和浏览器的高宽比，从而设置图片相应的宽和高：
```html
<img src="images/bg.jpg" id="bg" alt="">
```
css:
```css
#bg { position: fixed; top: 0; left: 0; }
.bgwidth { width: 100%; }
.bgheight { height: 100%; }
```
js:
```javascript
$(window).load(function() {
  var theWindow = $(window),
      $bg = $("#bg"),
      aspectRatio = $bg.width() / $bg.heightht();

    function resizeBg() {
      if ( (theWindow.width(var) / theWindow.height()) < aspectRatio ) {
        $bg
          .removeClass()
          .addClass('bgheight');
      } else {
        $bg
          .removeClass()
          .addClass('bgwidth');
      }
    }
    theWindow.resize(resizeBg).trigger("resize");
});
```
