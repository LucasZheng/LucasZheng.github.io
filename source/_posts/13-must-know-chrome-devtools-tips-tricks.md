title: chrome调试器中的13个技巧和窍门 
description: chrome调试器中的13个技巧和窍门
date: 2015-10-12 22:52:13
categories:
- other

tags: [debug]
---
Chrome浏览器的调试功能十分强大，今天介绍其中的13个技巧，这能提高你debug页面的效率。<!-- more -->
#### 文件快速转换
当DevTools打开时，按住`Ctrl+P`，mac上是`Cmd+P`，输入想要搜索的文件名称，就能打开对应项目中的文件。
![quick_switch_file](/images/quick_switch_file.gif)

#### 源码中搜索
如果想要项目中某个文件中特定的字符串，可以按住` Ctrl + Shift + F`，mac是`Cmd + Opt + F`，这种搜索模式支持正则匹配。
![search_within_source](/images/search_within_source.gif)

#### 定位到某行
当你在Sources中打开一个文件时，你可以按住`Ctrl + G`，输入行号来定位。
![go_to_line](/images/go_to_line.gif)

#### 在console中选择元素
DevTools中的console支持DOM元素的选择：
+ `$()`，是`document.querySelector()`的简写，返回匹配css规则的第一个元素。
+ `$$()`，是`document.querySelectorAll()`的简写，返回匹配css规则的元素集合。
+ `$0 – $4`，选择元素的历史纪录，$0代表最近的选择元素。

![select_element_in_console](/images/select_element_in_console.gif)

#### 多行编辑
当你打开一个文件时，可以按住`Ctrl`，mac上是`Cmd`，然后单击想要编辑的行，可以是多行，输入或删除文本。
![use_multiple_carets](/images/use_multiple_carets.gif)

#### 保留日志
在Console中，勾选`Preserve Log`，当你在页面间跳转时，Console中之前的log就会被保留，这对于你想查看之前的日志非常有用。
![preserve_log](/images/preserve_log.gif)

#### 格式化压缩代码
DevTools有一个功能可以格式化压缩后的代码，方便你调试线上的源码。
![pretty_source_code](/images/pretty_source_code.gif)

#### 设备仿真
在DevTools可以仿真移动设备，比如触摸，加速度计，甚至可以模拟定位。
![device_emulation](/images/device_emulation.gif)

#### 颜色拾取
在Styles中可以打开color picker，浮动光标到页面上时，可以显示对应的颜色。
![color_picker](/images/color_picker.gif)

#### 改变元素状态
在Styles中，可以勾选元素的各种状态来查看对应的样式。
![force_element_state](/images/force_element_state.gif)

#### 查看shadow DOM
![visualize_shadow_dom](/images/visualize_shadow_dom.gif)

#### 同时操作重复变量
当在Sources中编辑文件时，选择某个字符串后，多次按`Ctrl+D`，mac上是`Cmd+D`可以选择相邻的重复字符串，选定后可以同时进行编辑操作。
![select_next_occurrence](/images/select_next_occurrence.gif)

#### 改变颜色格式
在颜色预览框上，按住`Shift`，然后单击，可以在rgba，hsl和十六进制之间进行转换。
![change_color_format](/images/change_color_format.gif)

