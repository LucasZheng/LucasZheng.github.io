title: CSS3 Media Queries在iPhone和iPad上的运用
description: 如何使用css3 media query 来为特定的设备应用不同的样式规则
date: 2015-06-01 15:54:52

categories:
- css

tags: [css3, media]
---
大家对于css3中media属性并不陌生，但是随着一些高分辨率的设备面世，很多情况对于media的不标准的用法
也越来越多，下给总结出一些标准的设置的方法。<!-- more -->  
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
and (max-device-width : 568px) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPhone 5 横屏
{% codeblock lang:css %}
@media only screen
and (min-device-width : 320px)
and (max-device-width : 568px)
and (orientation : landscape) { 
	/* STYLES */
}
{% endcodeblock %}

#### iPhone 5 竖屏
{% codeblock lang:css %}
@media only screen
and (min-device-width : 320px)
and (max-device-width : 568px)
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