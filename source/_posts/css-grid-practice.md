title: css grid布局浅析与应用
description: css grid布局介绍及在项目中的使用
date: 2018-06-05 21:54:52

categories:
- css

tags: [css3, grid]
---
之前我们开发页面时常用的布局方法有表格(table)、浮动(float)、 定位(position)和内联块(inline-block)，这些方法本质上来讲都是hacks，存留了很多需要实现的重要功能问题(例如，垂直居中)。Flexbox的出现起到一定的补救作用，但是它只可以实现简单的一维布局，并不适用于复杂的二维布局(实际上 Flexbox 和 Grid 可以一起结合使用起到最佳效果)。<!-- more -->css grid是一个二维的网格布局系统，它能同时处理columns和rows，让我们能够更灵活的处理页面布局。

#### 容器的属性
以下属性设置在容器上。
+ display
+ grid-template-columns
+ grid-template-rows
+ grid-template-areas
+ grid-template
+ grid-column-gap
+ grid-row-gap
+ grid-gap
+ justify-items
+ align-items
+ place-items
+ justify-content
+ align-content
+ place-content
+ grid-auto-columns
+ grid-auto-rows
+ grid-auto-flow
+ grid

##### display属性
定义一个grid容器。

```css
.container {
  display: grid | inline-grid;
}
```
`grid`：block-level网格
`inline-grid`：inline-level网格

##### grid-template-columns和grid-template-rows属性
定义grid里面的columns和rows。
** track-size **可以是固定的长度、百分比或fr。
** line-name **是grid网格的某些别名，如：first、row1-start等。
格式如下：

```css
.container {
  grid-template-columns: <track-size> ... | <line-name> <track-size> ...;
  grid-template-rows: <track-size> ... | <line-name> <track-size> ...;
}
```
例如：

```css
.container {
  grid-template-columns: 40px 50px auto 50px 40px;
  grid-template-rows: 25% 100px auto;
}
```
通过line-name定义：

```css
.container {
  grid-template-columns: [first] 40px [line2] 50px [line3] auto [col4-start] 50px [five] 40px [end];
  grid-template-rows: [row1-start] 25% [row1-end] 100px [third-line] auto [last-line];
}
```
![](/images/grid-names.png)

网格线也有多个名称，如：

```css
.container {
  grid-template-rows: [row1-start] 25% [row1-end row2-start] 25% [row2-end];
}
```
如果有重复的网格，可以使用`repeat()`：

```css
.container {
  grid-template-columns: repeat(3, 20px [col-start]);
}
```
等价于：

```css
.container {
  grid-template-columns: [col-start] 20px [col-start] 20px [col-start] 20px;
}
```
`fr`是grid引入的单位，它将grid内剩余的空间用`fr`来划分，如下每个项目会占据容器宽度额三分之一。

```css
.container {
  grid-template-columns: 1fr 1fr 1fr;
}
```
`fr`不会把** non-flexible  **元素占据的空间计算在内。如下`fr`计算的总空间不包括50px。

```css
.container {
  grid-template-columns: 1fr 50px 1fr 1fr;
}
```
##### grid-template-areas属性
采用可视化的方式指定每个单元格的名称，点代表一个空的单元格。
** grid-area-name **：网格名称。
** `.` **：空单元格。
** none **：非网格布局。
格式如下：
```css
.container {
  grid-template-areas:
    "<grid-area-name> | . | none | ..."
    "...";
}
```

例如：
```css
.item-a {
  grid-area: header;
}
.item-b {
  grid-area: main;
}
.item-c {
  grid-area: sidebar;
}
.item-d {
  grid-area: footer;
}

.container {
  grid-template-columns: 50px 50px 50px 50px;
  grid-template-rows: auto;
  grid-template-areas:
    "header header header header"
    "main main . sidebar"
    "footer footer footer footer";
}
```
![](/images/grid-template-areas.png)

##### grid-template属性
它是`grid-template-rows`、`grid-template-columns`和`grid-template-areas`的简写。
** none **：设置三个属性的初始值。
** <grid-template-rows> / <grid-template-columns> **：分别设置rows和columns。
格式如下：

```css
.container {
  grid-template: none | <grid-template-rows> / <grid-template-columns>;
}
```
例如合并** grid-template **的三个属性：

```css
.container {
  grid-template:
    [row1-start] "header header header" 25px [row1-end]
    [row2-start] "footer footer footer" 25px [row2-end]
    / auto 50px auto;
}
```
等价于：

```css
.container {
  grid-template-rows: [row1-start] 25px [row1-end row2-start] 25px [row2-end];
  grid-template-columns: auto 50px auto;
  grid-template-areas:
    "header header header"
    "footer footer footer";
}
```
##### grid-column-gap和grid-row-gap属性
设置columns和rows之间的间隔。
** line-size **：间隔大小。
格式如下：

```css
.container {
  grid-column-gap: <line-size>;
  grid-row-gap: <line-size>;
}
```
例如：

```css
.container {
  grid-template-columns: 100px 50px 100px;
  grid-template-rows: 80px auto 80px;
  grid-column-gap: 10px;
  grid-row-gap: 15px;
}
```
![](/images/grid-column-row-gap.png)

##### grid-gap属性
`grid-row-gap and`和`grid-column-gap`属性的缩写。
格式如下：

```css
.container {
  grid-gap: <grid-row-gap> <grid-column-gap>;
}
```
例如：

```css
.container {
  grid-template-columns: 100px 50px 100px;
  grid-template-rows: 80px auto 80px;
  grid-gap: 15px 10px;
}
```
如果只指定了一个值的话，则colums和rows的间隔相同。

##### justify-items属性
所有单元格在row方向上的对齐方式。
格式如下：

```css
.container {
  justify-items: start | end | center | stretch;
}
```
例如：

```css
.container {
  justify-items: start;
}
```
![](/images/grid-justify-items-start.png)

```css
.container {
  justify-items: end;
}
```
![](/images/grid-justify-items-end.png)

```css
.container {
  justify-items: center;
}
```
![](/images/grid-justify-items-center.png)

```css
.container {
  justify-items: stretch;
}
```
![](/images/grid-justify-items-stretch.png)

##### align-items属性
所有单元格在column方向上的对齐方式。
格式如下：

```css
.container {
  align-items: start | end | center | stretch;
}
```
例如：

```css
.container {
  align-items: start;
}
```
![](/images/grid-align-items-start.png)

```css
.container {
  align-items: end;
}
```
![](/images/grid-align-items-end.png)


```css
.container {
  align-items: center;
}
```
![](/images/grid-align-items-center.png)


```css
.container {
  align-items: stretch;
}
```
![](/images/grid-align-items-stretch.png)

##### place-items属性
** align-items ** 和 ** justify-items **的简写
格式如下：

```css
.container {
  place-items: <align-items> / <justify-items>;
}
```
如果只有一个值，则** align-items ** 和 ** justify-items **都设置为这个值。

##### justify-content属性
如果所有单元格相加总的宽度小于grid容器的宽度，则用** justify-content ** 属性来决定对齐方式
格式如下：

```css
.container {
  justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
}
```
例如：

```css
.container {
  justify-content: start;
}
```
![](/images/grid-justify-content-start.png)

```css
.container {
  justify-content: end;
}
```
![](/images/grid-justify-content-end.png)

```css
.container {
  justify-content: center;
}
```
![](/images/grid-justify-content-center.png)

```css
.container {
  justify-content: stretch;
}
```
![](/images/grid-justify-content-stretch.png)

```css
.container {
  justify-content: space-around;
}
```
![](/images/grid-justify-content-space-around.png)

```css
.container {
  justify-content: space-between;
}
```
![](/images/grid-justify-content-space-between.png)

```css
.container {
  justify-content: space-evenly;
}
```
![](/images/grid-justify-content-space-evenly.png)

##### align-content属性
如果所有单元格相加总的高度小于grid容器的高度，则用** align-content ** 属性来决定对齐方式
格式如下：

```css
.container {
  align-content: start | end | center | stretch | space-around | space-between | space-evenly;
}
```
例如：

```css
.container {
  align-content: start;
}
```
![](/images/grid-align-content-start.png)

```css
.container {
  align-content: end;
}
```
![](/images/grid-align-content-end.png)

```css
.container {
  align-content: center;
}
```
![](/images/grid-align-content-center.png)

```css
.container {
  align-content: stretch;
}
```
![](/images/grid-align-content-stretch.png)

```css
.container {
  align-content: space-around;
}
```
![](/images/grid-align-content-space-around.png)

```css
.container {
  align-content: space-between;
}
```
![](/images/grid-align-content-space-between.png)

```css
.container {
  align-content: space-evenly;
}
```
![](/images/grid-align-content-space-evenly.png)

##### place-content属性
** align-content ** 和 ** justify-content **属性的简写。

格式如下：

```css
.container {
  place-content: <align-content> <justify-content>;
}
```
如果只指定了一个值的话，则align-content和justify-content设置为相同的值。

##### grid-auto-columns 和 grid-auto-rows属性
指定以何种方式扩展grid网格。
** track-size **：可以是固定长度、百分比或`fr`。
格式如下：

```css
.container {
  grid-auto-columns: <track-size> ...;
  grid-auto-rows: <track-size> ...;
}
```
假设有如下网格：

```css
.container {
  grid-template-columns: 60px 60px;
  grid-template-rows: 90px 90px
}
.item-a {
  grid-column: 1 / 2;
  grid-row: 2 / 3;
}
.item-b {
  grid-column: 5 / 6;
  grid-row: 2 / 3;
}
```
我们只指定了2 x 2的网格，而对于`item-b`需要自动扩展网格才能定位。
```css
.container {
  grid-auto-columns: 60px;
}
```
![](/images/implicit-tracks-with-widths.png)

##### grid-auto-flow属性
如果你的grid元素没有指定位置，则`grid-auto-flow`属性将决定它的显示方式。
** row **：按行的方式排列。
** column **：按列的方式排列。
** dense **：根据网格的内容大小改变显示顺序，不推荐使用。
格式如下：

```css
.container {
  grid-auto-flow: row | column | row dense | column dense
}
```
例如：

```css
<section class="container">
  <div class="item-a">item-a</div>
  <div class="item-b">item-b</div>
  <div class="item-c">item-c</div>
  <div class="item-d">item-d</div>
  <div class="item-e">item-e</div>
</section>
.container {
  display: grid;
  grid-template-columns: 60px 60px 60px 60px 60px;
  grid-template-rows: 30px 30px;
  grid-auto-flow: row;
}
.item-a {
  grid-column: 1;
  grid-row: 1 / 3;
}
.item-e {
  grid-column: 5;
  grid-row: 1 / 3;
}
```
![](/images/grid-auto-flow-row.png)
采用column排列：
```css
.container {
  display: grid;
  grid-template-columns: 60px 60px 60px 60px 60px;
  grid-template-rows: 30px 30px;
  grid-auto-flow: column;
}
```
![](/images/grid-auto-flow-column.png)

##### grid属性
集合`grid-template-rows`、`grid-template-columns`、`grid-template-areas`、`grid-auto-rows`、`grid-auto-columns`和`grid-auto-flow`的缩写
例如：

```css
.container {
  grid: 100px 300px / 3fr 1fr;
}
```
等价于

```css
.container {
  grid-template-rows: 100px 300px;
  grid-template-columns: 3fr 1fr;
}
```
又如：

```css
.container {
  grid: auto-flow / 200px 1fr;
}
```
等价于

```css
.container {
  grid-auto-flow: row;
  grid-template-columns: 200px 1fr;
}
```
又如：

```css
.container {
  grid: auto-flow dense 100px / 1fr 2fr;
}
```
等价于

```css
.container {
  grid-auto-flow: row dense;
  grid-auto-rows: 100px;
  grid-template-columns: 1fr 2fr;
}
```
又如：

```css
.container {
  grid: 100px 300px / auto-flow 200px;
}
```
等价于

```css
.container {
  grid-template-rows: 100px 300px;
  grid-auto-flow: column;
  grid-auto-columns: 200px;
}
```
再复杂点的：

```css
.container {
  grid: [row1-start] "header header header" 1fr [row1-end]
        [row2-start] "footer footer footer" 25px [row2-end]
        / auto 50px auto;
}
```
等价于

```css
.container {
  grid-template-areas:
    "header header header"
    "footer footer footer";
  grid-template-rows: [row1-start] 1fr [row1-end row2-start] 25px [row2-end];
  grid-template-columns: auto 50px auto;
}
```
#### grid子元素的属性
以下属性设置在grid子元素上。
+ grid-column-start
+ grid-column-end
+ grid-row-start
+ grid-row-end
+ grid-column
+ grid-row
+ grid-area
+ justify-self
+ align-self
+ place-self

##### grid-column-start、grid-column-end、grid-row-start和grid-row-end属性
这四个属性用来决定grid子元素以什么基准定位。
** line **：网格线的名称
** span<number> **：grid子元素跨域多少网格
** span<line> **：grid子元素跨域网格，以grid line为基准
** auto **：默认值
格式如下：

```css
.item {
  grid-column-start: <number> | <name> | span <number> | span <name> | auto
  grid-column-end: <number> | <name> | span <number> | span <name> | auto
  grid-row-start: <number> | <name> | span <number> | span <name> | auto
  grid-row-end: <number> | <name> | span <number> | span <name> | auto
}
```
例如：

```css
.item-a {
  grid-column-start: 2;
  grid-column-end: five;
  grid-row-start: row1-start
  grid-row-end: 3
}
```
![](/images/grid-start-end-a.png)

又如：

```css
.item-b {
  grid-column-start: 1;
  grid-column-end: span col4-start;
  grid-row-start: 2
  grid-row-end: span 2
}
```
![](/images/grid-start-end-b.png)

如果`grid-column-end/grid-row-end`没有声明，则默认占据一个网格。
##### grid-column和grid-row属性
分别是`grid-column-start grid-column-end`和`grid-row-start grid-row-end`的缩写。
格式如下：

```css
.item {
  grid-column: <start-line> / <end-line> | <start-line> / span <value>;
  grid-row: <start-line> / <end-line> | <start-line> / span <value>;
}
```
例如：
```css
.item-c {
  grid-column: 3 / span 2;
  grid-row: third-line / 4;
}
```
![](/images/grid-start-end-c.png)

如果没有指定终止线，则默认占据一个网格。
##### grid-area属性
通过`grid-template-areas`定义的area来定位子元素。
格式如下：

```css
.item {
  grid-area: <name> | <row-start> / <column-start> / <row-end> / <column-end>;
}
```
例如：

```css
.item-d {
  grid-area: header
}
```
也可以是`grid-row-start grid-column-start grid-row-end grid-column-end`的缩写。

```css
.item-d {
  grid-area: 1 / col4-start / last-line / 6
}
```
![](/images/grid-start-end-d.png)

##### justify-self属性
和`justify-items`属性功能相同，只不过该属性作用于单个网格。
格式如下：

```css
.item {
  justify-self: start | end | center | stretch;
}
```
例如：

```css
.item-a {
  justify-self: start;
}
```
![](/images/grid-justify-self-start.png)

例如：

```css
.item-a {
  justify-self: end;
}
```
![](/images/grid-justify-self-end.png)

例如：

```css
.item-a {
  justify-self: center;
}
```
![](/images/grid-justify-self-center.png)

例如：

```css
.item-a {
  justify-self: stretch;
}
```
![](/images/grid-justify-self-stretch.png)

##### align-self属性
和`align-items`属性功能相同，只不过该属性作用于单个网格。
格式如下：

```css
.item {
  align-self: start | end | center | stretch;
}
```
例如：

```css
.item-a {
  align-self: start;
}
```
![](/images/grid-align-self-start.png)

例如：

```css
.item-a {
  align-self: end;
}
```
![](/images/grid-align-self-end.png)

例如：

```css
.item-a {
  align-self: center;
}
```
![](/images/grid-align-self-center.png)

例如：

```css
.item-a {
  align-self: stretch;
}
```
![](/images/grid-align-self-stretch.png)

##### place-self属性
改属性用来同时设置`align-self` 和 `justify-self`。
** auto  **：默认值
** <align-self> / <justify-self>  **: 分别设置两个属性，如果只有一个值，则两个属性值相同。
例如：

```css
.item-a {
  place-self: center;
}
```
![](/images/grid-place-self-center.png)

例如：

```css
.item-a {
  place-self: center stretch;
}
```
![](/images/grid-align-self-center.png)

#### CanIUse:
截止当前css grid的浏览器支持情况如下：
![](/images/css-grid-caniuse.png)

#### grid实践
下面结合实际项目介绍下grid如何使用（由于目前的兼容问题，暂未考虑IE浏览器）。
PC端页面网格布局如下：
![](/images/css-grid-practice-login.png)

html结构大致如下：
```html
<section class="login-container">
  <div class="logo"></div>
  <el-form>
    <h3 class="title">登录 LOGIN</h3>
    <el-form-item class="oa-input">
      <el-input placeholder="请输入账号" />
      <i class="iconfont icon-username"></i>
    </el-form-item>
    <el-form-item class="pwd-input">
      <el-input placeholder="请输入密码" />
        <i class="iconfont icon-password"></i>
    </el-form-item>
    <el-button type="primary">登录</el-button>
  </el-form>
  <p class="copy">Copyright &copy; {{ year }}</p>
</section>
```
定义容器的规则：
```css
.login-container {
  display: grid;
  grid-template: 10% auto 10% / 3fr 4fr 3fr;
  grid-template-areas:
    "logo . ."
    ". form ."
    ". copy .";
  height: 100vh;
  background: $bg;
  background-size: 100% 100%;
}
```
容器下个子元素的规则：
```css
.logo {
  grid-area: logo;
}
.login-form {
  display: grid;
  place-items: center;
  grid-template: 28% 22% 22% 28% / auto 320px auto;
  grid-template-areas:
  ". title ."
  ". oa-input ."
  ". pwd-input ."
  ". login-btn .";
  grid-area: form;
  place-self: center;
}
.copy {
  grid-area: copy;
  place-self: center;
}
.title {
  grid-area: title;
}
.oa-input {
  grid-area: oa-input;
}
.pwd-input {
  grid-area: pwd-input;
}
.el-form-item {
  align-self: stretch;
}
.el-button {
  grid-area: login-btn;
}
```

对于移动端采用`@media`属性来改变网格的布局。
```scss
@media (max-width: 767px) {
  .login-container {
    grid-template: 10% auto 10% / auto;
    grid-template-areas:
    "logo"
    "form"
    "copy";
    .logo {
      grid-area: logo;
    }
    .login-form {
      grid-area: form;
    }
    .copy {
      grid-area: copy;
    }
  }
}
```
![](/images/css-grid-practice-login-mobile.png)
