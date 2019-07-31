title: Vue开发记录之props属性
description: Vue开发记录之props属性。
date: 2019-07-31 20:27:46

categories:
- mvc

tags: [javascript, vue]

---
在用VUE开发后台管理系统时，经常需要新增或修改一条记录，而它们的模板往往是复用的，只是新增时模板中的表单数据为空，而修改时表单中数据为该条记录已有的数据。<!-- more -->
在项目开发中，我们抽取一个独立的子组件用来实现新增或编辑功能，在父级页面中引入该子组件，利用子组件中props属性下的default字段来判断是新增或修改，从而给子组件绑定数据。

#### 实例
如下图，我们在列表页面有一个`新增`按钮，每条记录的后面有个`修改`图标，点击它们都会弹出一个浮层用于新增或编辑记录。

父级列表页面：
![](/images/props_list.png)

修改弹窗页面：
![](/images/props_edit.png)

新增弹窗页面：
![](/images/props_add.png)

列表页面大概逻辑如下：
![](/images/props_list_vue.png)

在初始和新增状态下，将`modifyData`设置为`undefined`，修改记录时`modifyData`为记录的详情数据，通过props方法将数据传递给`topic-dialog`子组件。

子组件`topic-dialog`大概逻辑如下：
![](/images/props_dialog_vue.png)

由于我们不能直接编辑props传递的数据，故将`this.data`赋值给`formData`属性。同时props中的data字段设置default数据。当data为`undefined`即新增时，使用default中设置的数据，否则为记录的详情数据。

此处需要注意，父组件中props传递的字段只能设置为`undefined`时，子组件中对应的字段才能使用`default`设置的数据。设置为空或为`null`时，`default`均不生效。通过查看如下的VUE源码`src\core\util\props.js`：

![](/images/props_source.png)

当传递的字段值为`undefined`时，才会去获取defalut value。

