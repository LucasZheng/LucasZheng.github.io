title: css之focus-within
description: css之focus-within伪类
date: 2017-08-03 21:54:52

categories:
- css

tags: [css3, css]
---
`:focus-within`伪类能够在当前元素或其后代任何元素在`focus`状态时应用相应的规则。语言上比较难理解，可以直接看下面的例子：<!-- more -->
CSS:
```css
.name-container {
  padding: 4px;
}

.name-container:focus-within {
  background: yellow;
}

input {
  margin: 4px;
}
```

HTML:
```html
<p>The div below will have a yellow background if
    either input inside is focused.</p>

<div class="name-container">
  <label for="given_name">
    Given Name:
    <input id="given_name" type="text">
  </label>
  <label for="family_name">
    Family Name:
    <input id="family_name" type="text">
  </label>
</div>
```

渲染结果：
![](/images/focus-within.png)

应用条件：
当前元素或后代任何元素具备`focus`能力都能触发这个伪类规则，且触发形式不限，可以是通过**点击**、**tab**甚至是通过js来让某个元素`focus`。

应用场景：
编辑表单时，让整个表单应用某些样式规则

CanIUse:
![](/images/focus-within-caniuse.png)
