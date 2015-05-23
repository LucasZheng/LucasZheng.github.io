title: web性能优化之－－图片
date: 2015-03-20 09:47:51
description: web 性能优化中很重要的一项就是图片优化，文章对当前三种处理图片icon的方式进行了比较
categories:
- performance
tags: [performance, image sprites, font icons, SVG icons]
---
当前有许多方法来提升你的网站性能。其中用的比较多的就是image sprites或font icons，其次SVG icons也逐渐成为将来发展的趋势。这些图片处理方式能够大大减少你的页面加载时间。但是在你选择哪一种优化方案前，<!-- more -->你得先了解这个方案的优点和缺点。
#### Image Sprites
Image sprites可以让你把大量的图片或icon合并成一个文件，这些图片或icon可以是任意形状或大小。
** 优点 **
最大的优点就是将众多的图片或icon合并成一个文件，页面只需要有一个http请求。同时各个浏览器都支持这种图片处理方式。  
** 缺点 **  
Image sprites虽然能减少http请求，但合并后的图片文件比单个图片文件要大，因此http请求这个合并后的图片所需要的时间就比较长。如果页面首次加载一个很大的图片，你会明显的感觉到图片显示的视觉差。当然你也可以等图片加载完成后才显示整个页面。  
如果你是通过手动方式来合并图片的话，那么以后如果你改变其中一个图片的宽带或高度，你还需要重新替换这个新生成的图片，同时需要修改你对应的css代码。如果你经常修改或更换图片的话，这就比较浪费时间，当然也可以通过自动化工具来做，比如[Grunt](http://gruntjs.com/)。  
Image sprites还会增加你的css代码量，这是必不可少的，因为你需要在css中定位你所需要的某个图片。比较好的一个解决方案就是用一个class来实现backgroud-image，用其它class来实现backgroud-position。  
{% codeblock lang:css %}
.sprite {
    background: url(pathtoimage/sprite.png) no-repeat;
}
.image-1 {
    background-position: -120px 0;
    width: 40px; height: 40px;
}
.image-2 {
    background-position: -120px -40px;
    width: 40px; height: 40px;
}
{% endcodeblock %}

html:  
{% codeblock lang:html %}
<span class="sprite image-1"></span>
{% endcodeblock %}

#### Font Icons
Font Icons在行为上更像fonts。它通过CSS3的@font-face来声明。  
** 优点 **
在尺寸大小方面，Font icons要比image小很多，这对于提升网站性能而言很有必要。  
因为Font icons很像fonts，所以你可以通过css改变它的颜色、大小。还可以通过css添加hover态的样式。如果有必要还可以添加text-shadow来增强视觉效果。  
{% codeblock lang:css %}
@font-face {
    font-family: 'iconfont';
    src:url('fonts/iconfont.eot');
    src:url('fonts/iconfont.eot?#iefix') format('embedded-opentype'),
        url('fonts/iconfont.woff') format('woff'),
        url('fonts/iconfont.ttf') format('truetype'),
        url('fonts/iconfont.svg#tetzlaff') format('svg');
    font-weight: normal;
    font-style: normal;
}
{% endcodeblock %}

没有必要担心Font icons的浏览器兼容性。如果你有各种格式的文件(eot、ttf、woff和svg)，你就可以像font一样通过@font-face来使用。如果你没有全部格式的文件，也可以通过[Font-Squirrels font-face generator](http://www.fontsquirrel.com/tools/webfont-generator)生成。 其它创建icon的资源有：[IcoMoon](https://icomoon.io/)、[Pictos](http://pictos.cc/)、[Fontastic](http://fontastic.me/)。 
** 缺点 **  
Font icons的图形比较简单，不能过于复杂，颜色单一。如果你需要色彩更丰富、视觉效果更好的体验，最好还是用images。
#### SVG Icons
SVG已经出现很长一段时间了，但并没有引起广泛使用。我会用单独一篇文章来说明采用SVG的好处，特别是对于响应式设计。它的优点大概有如下几点：  
－ icon可以任意尺寸放大缩小，质量保持不变
－ 可以多种颜色组合（Font Icons只能单一颜色）
－ 可以使用动画效果
SVG在实现上要比其它两种复杂点，和常规css比起来，它们有自己的语法规则（比如在css中用：`background:red`;SVG中用：`fill:red`）。  
#### 如何选择
选择哪种方式依赖于你所处的环境。如果你需要信息更丰富的图片，而不仅仅是icons，那么就选择Image Sprites。否则，如果只需要一些简单的icons，那么就采用Font Icons。如果涉及到响应式设计，可以考虑采用SVG Icons。当然在开发过程中，一种单一的方案可能不能满足所有的需求，可能需要几种方案的组合形式。
