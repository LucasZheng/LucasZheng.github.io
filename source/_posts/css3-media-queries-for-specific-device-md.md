title: CSS3 Media Queries在各种设备上的应用
description: 如何使用css3 media query 来为特定的设备应用不同的样式规则
date: 2015-06-01 15:54:52

categories:
- css

tags: [css3, media]
---
大家对于css3中media属性并不陌生，但是随着一些高分辨率的设备面世，很多情况对于media的不标准的用法也越来越多，下给总结出一些标准的设置的方法。<!-- more -->  
#### 所有 iPad Media Queries
{% codeblock lang:css %}
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPad 横屏
{% codeblock lang:css %}
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px)
and (orientation : landscape) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPad 竖屏
{% codeblock lang:css %}
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px)
and (orientation : portrait) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPad 3 & 4 的 Media Queries
{% codeblock lang:css %}
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px)
and (-webkit-min-device-pixel-ratio: 2) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPad 3 & 4 的 横屏
{% codeblock lang:css %}
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px)
and (orientation : landscape)
and (-webkit-min-device-pixel-ratio: 2) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPad 3 & 4 的 竖屏
{% codeblock lang:css %}
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px)
and (orientation : portrait)
and (-webkit-min-device-pixel-ratio: 2) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPad 1 & 2 Media Queries
{% codeblock lang:css %}
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px)
and (-webkit-min-device-pixel-ratio: 1) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPad 1 & 2 横屏
{% codeblock lang:css %}
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px)
and (orientation : landscape)
and (-webkit-min-device-pixel-ratio: 1) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPad 1 & 2 竖屏
{% codeblock lang:css %}
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px)
and (orientation : portrait)
and (-webkit-min-device-pixel-ratio: 1) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPad mini 也是和 iPad 1 和 2 一样
#### iPhone 5 Media Queries
{% codeblock lang:css %}
@media only screen
and (min-device-width : 320px)
and (max-device-width : 568px) 
and (-webkit-min-device-pixel-ratio: 2) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPhone 5 横屏
{% codeblock lang:css %}
@media only screen
and (min-device-width : 320px)
and (max-device-width : 568px)
and (-webkit-min-device-pixel-ratio: 2)
and (orientation : landscape) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPhone 5 竖屏
{% codeblock lang:css %}
@media only screen
and (min-device-width : 320px)
and (max-device-width : 568px)
and (-webkit-min-device-pixel-ratio: 2)
and (orientation : portrait) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPhone 2G, 3G, 4, 4S Media Queries
{% codeblock lang:css %}
@media only screen
and (min-device-width : 320px)
and (max-device-width : 480px) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPhone 2G-4S 横屏
{% codeblock lang:css %}
@media only screen
and (min-device-width : 320px)
and (max-device-width : 480px)
and (orientation : landscape) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPhone 4 和4s
```css
/* Portrait and Landscape */
@media only screen 
  and (min-device-width: 320px) 
  and (max-device-width: 480px)
  and (-webkit-min-device-pixel-ratio: 2) {

}

/* Portrait */
@media only screen 
  and (min-device-width: 320px) 
  and (max-device-width: 480px)
  and (-webkit-min-device-pixel-ratio: 2)
  and (orientation: portrait) {
}

/* Landscape */
@media only screen 
  and (min-device-width: 320px) 
  and (max-device-width: 480px)
  and (-webkit-min-device-pixel-ratio: 2)
  and (orientation: landscape) {
}
```

#### iphone 6
```css
/* Portrait and Landscape */
@media only screen 
  and (min-device-width: 375px) 
  and (max-device-width: 667px) 
  and (-webkit-min-device-pixel-ratio: 2) { 

}

/* Portrait */
@media only screen 
  and (min-device-width: 375px) 
  and (max-device-width: 667px) 
  and (-webkit-min-device-pixel-ratio: 2)
  and (orientation: portrait) { 

}

/* Landscape */
@media only screen 
  and (min-device-width: 375px) 
  and (max-device-width: 667px) 
  and (-webkit-min-device-pixel-ratio: 2)
  and (orientation: landscape) { 
}
```

#### iPhone 6+
```css
/* Portrait and Landscape */
@media only screen 
  and (min-device-width: 414px) 
  and (max-device-width: 736px) 
  and (-webkit-min-device-pixel-ratio: 3) { 

}

/* Portrait */
@media only screen 
  and (min-device-width: 414px) 
  and (max-device-width: 736px) 
  and (-webkit-min-device-pixel-ratio: 3)
  and (orientation: portrait) { 

}

/* Landscape */
@media only screen 
  and (min-device-width: 414px) 
  and (max-device-width: 736px) 
  and (-webkit-min-device-pixel-ratio: 3)
  and (orientation: landscape) { 
}
```

#### Galaxy Phones
```css
/* ----------- Galaxy S3 ----------- */

/* Portrait and Landscape */
@media screen 
  and (device-width: 320px) 
  and (device-height: 640px) 
  and (-webkit-device-pixel-ratio: 2) {
}

/* Portrait */
@media screen 
  and (device-width: 320px) 
  and (device-height: 640px) 
  and (-webkit-device-pixel-ratio: 2) 
  and (orientation: portrait) {
}

/* Landscape */
@media screen 
  and (device-width: 320px) 
  and (device-height: 640px) 
  and (-webkit-device-pixel-ratio: 2) 
  and (orientation: landscape) {
}

/* ----------- Galaxy S4 ----------- */

/* Portrait and Landscape */
@media screen 
  and (device-width: 320px) 
  and (device-height: 640px) 
  and (-webkit-device-pixel-ratio: 3) {
}

/* Portrait */
@media screen 
  and (device-width: 320px) 
  and (device-height: 640px) 
  and (-webkit-device-pixel-ratio: 3) 
  and (orientation: portrait) {
}

/* Landscape */
@media screen 
  and (device-width: 320px) 
  and (device-height: 640px) 
  and (-webkit-device-pixel-ratio: 3) 
  and (orientation: landscape) {
}

/* ----------- Galaxy S5 ----------- */

/* Portrait and Landscape */
@media screen 
  and (device-width: 360px) 
  and (device-height: 640px) 
  and (-webkit-device-pixel-ratio: 3) {
}

/* Portrait */
@media screen 
  and (device-width: 360px) 
  and (device-height: 640px) 
  and (-webkit-device-pixel-ratio: 3) 
  and (orientation: portrait) {
}

/* Landscape */
@media screen 
  and (device-width: 360px) 
  and (device-height: 640px) 
  and (-webkit-device-pixel-ratio: 3) 
  and (orientation: landscape) {
}
```

#### HTC Phones
```css
/* ----------- HTC One ----------- */

/* Portrait and Landscape */
@media screen 
  and (device-width: 360px) 
  and (device-height: 640px) 
  and (-webkit-device-pixel-ratio: 3) {

}

/* Portrait */
@media screen 
  and (device-width: 360px) 
  and (device-height: 640px) 
  and (-webkit-device-pixel-ratio: 3) 
  and (orientation: portrait) {

}

/* Landscape */
@media screen 
  and (device-width: 360px) 
  and (device-height: 640px) 
  and (-webkit-device-pixel-ratio: 3) 
  and (orientation: landscape) {

}
```

#### Galaxy Tablets
```css
/* ----------- Galaxy Tab 10.1 ----------- */

/* Portrait and Landscape */
@media 
  (min-device-width: 800px) 
  and (max-device-width: 1280px) {

}

/* Portrait */
@media 
  (max-device-width: 800px) 
  and (orientation: portrait) { 

}

/* Landscape */
@media 
  (max-device-width: 1280px) 
  and (orientation: landscape) { 

}
```

#### Nexus Tablets
```css
/* ----------- Asus Nexus 7 ----------- */

/* Portrait and Landscape */
@media screen 
  and (device-width: 601px) 
  and (device-height: 906px) 
  and (-webkit-min-device-pixel-ratio: 1.331) 
  and (-webkit-max-device-pixel-ratio: 1.332) {

}

/* Portrait */
@media screen 
  and (device-width: 601px) 
  and (device-height: 906px) 
  and (-webkit-min-device-pixel-ratio: 1.331) 
  and (-webkit-max-device-pixel-ratio: 1.332) 
  and (orientation: portrait) {

}

/* Landscape */
@media screen 
  and (device-width: 601px) 
  and (device-height: 906px) 
  and (-webkit-min-device-pixel-ratio: 1.331) 
  and (-webkit-max-device-pixel-ratio: 1.332) 
  and (orientation: landscape) {

}
```

#### Kindle Fire
```css
/* ----------- Kindle Fire HD 7" ----------- */

/* Portrait and Landscape */
@media only screen 
  and (min-device-width: 800px) 
  and (max-device-width: 1280px) 
  and (-webkit-min-device-pixel-ratio: 1.5) {

}

/* Portrait */
@media only screen 
  and (min-device-width: 800px) 
  and (max-device-width: 1280px) 
  and (-webkit-min-device-pixel-ratio: 1.5) 
  and (orientation: portrait) {
}

/* Landscape */
@media only screen 
  and (min-device-width: 800px) 
  and (max-device-width: 1280px) 
  and (-webkit-min-device-pixel-ratio: 1.5) 
  and (orientation: landscape) {

}

/* ----------- Kindle Fire HD 8.9" ----------- */

/* Portrait and Landscape */
@media only screen 
  and (min-device-width: 1200px) 
  and (max-device-width: 1600px) 
  and (-webkit-min-device-pixel-ratio: 1.5) {

}

/* Portrait */
@media only screen 
  and (min-device-width: 1200px) 
  and (max-device-width: 1600px) 
  and (-webkit-min-device-pixel-ratio: 1.5) 
  and (orientation: portrait) {
}

/* Landscape */
@media only screen 
  and (min-device-width: 1200px) 
  and (max-device-width: 1600px) 
  and (-webkit-min-device-pixel-ratio: 1.5) 
  and (orientation: landscape) {

}
```

#### Laptops
```css
/* ----------- Non-Retina Screens ----------- */
@media screen 
  and (min-device-width: 1200px) 
  and (max-device-width: 1600px) 
  and (-webkit-min-device-pixel-ratio: 1) { 
}

/* ----------- Retina Screens ----------- */
@media screen 
  and (min-device-width: 1200px) 
  and (max-device-width: 1600px) 
  and (-webkit-min-device-pixel-ratio: 2)
  and (min-resolution: 192dpi) { 
}
```

#### Appale Watch
```css
/* ----------- Apple Watch ----------- */
@media
  (max-device-width: 42mm)
  and (min-device-width: 38mm) { 

}
```

#### Moto 360 Watch
```css
/* ----------- Moto 360 Watch ----------- */
@media 
  (max-device-width: 218px)
  and (max-device-height: 281px) { 

}
```

对于其他不常见的设备可以参考这里：[其他设备查询](http://cssmediaqueries.com/target/)
