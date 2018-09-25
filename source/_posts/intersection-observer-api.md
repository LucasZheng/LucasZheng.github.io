title: Intersection Observer API使用介绍
description: Intersection Observer API使用方法及使用场景介绍
date: 2018-09-12 12:52:13
categories:
- javascript

tags: [javascript]
---
Intersection observer API提供了一种基于异步的方式来观察目标元素与父元素或根元素相交的变化检测。<!-- more -->通俗的讲就是检测一个元素视图是否和它的父元素视图或根元素（浏览器视窗）重叠，重叠的比例有多少，但这种检测方式是异步实现的，不影响网站的使用体验。

在日常开发中，有许多应用场景需要用到这种检测方式，比如：
+ 当页面滚动时，懒加载图片或其他内容；
+ 实现“可无限滚动”网站，也就是当用户滚动网页时直接加载更多内容，无需翻页；
+ 为计算广告收益，检测其广告元素的曝光情况；
+ 根据用户是否已滚动到相应区域来灵活开始执行任务或动画；

可以通过[Can I Use](https://caniuse.com/#search=intersection)查询Intersection observer API的浏览器兼容情况，截止到目前各主流浏览器的兼容如下：
![intersection_observer_browser_support](/images/intersection_observer_browser_support.png)

过去，检测元素是否出现在特定区域内往往需要不断的监听scroll事件，根据计算的特定规则（文档高度 - 视口高度 - 滚动条滚动高度 < 某个特定的值）来执行相应的任务（如向后端请求数据）。可是这些代码都在主线程上运行，任何一点计算开销都可能造成性能问题。当网页遍布这些代码时就显得比较丑陋了。

#### Intersection observer用法
Intersection observer 允许你配置一个回调函数，每当target元素和设备视口或者其他指定元素发生交集的时候该回调函数将会被执行。如果设置root参数为null或者不设置则root默认为设备视口，每当tartget元素在root元素中的可见性交集数量发生变化(target元素的位置在root中发生变化)都会执行你提供的回调函数。

创建一个 IntersectionObserver对象并传入相应参数和回调用函数。

```javascript
const options = {
  root: document.querySelector('#scrollArea'),
  rootMargin: '0px',
  threshold: 1.0
}
const callback = (entries, observer) => {
    /* 回调任务 */
}
const observer = new IntersectionObserver(callback, options)
```

Intersection observer参数说明：

`root`

与target元素将要相交的父元素，如果root参数指定为null或者不指定的时候默认使用浏览器视口做为root。

`rootMargin`

root元素的外边距。如果有指定root参数，则rootMargin也可以使用百分比来取值。该属性值是用作root元素和target元素发生交集时候的计算交集的区域范围，使用该属性可以控制root元素每一边的收缩或者扩张。默认值为0。

`threshold`

可以是单一的number也可以是number数组，target元素和root元素相交程度达到该值的时候Intersection Observer注册的回调函数将会被执行。如果你只是想要探测当target元素的在root元素中的可见性超过50%的时候，你可以指定该属性值为0.5。如果你想要target元素在root元素的可见程度每多25%就执行一次回调，那么你可以指定一个数组[0, 0.25, 0.5, 0.75, 1]。默认值是0(意味着只要有一个target像素出现在root元素中，回调函数将会被执行)。该值为1.0含义是当target完全出现在root元素中时候回调才会被执行。

配置观察者对象：

```javascript
const target = document.querySelector('#listItem')
observer.observe(target)

// 移除一个监听，移除之后，target元素的可视区域变化，将不再触发前面的回调函数
observer.unobserve(target)

// 停止所有的监听
observer.disconnect()
```

抽取公共组件(Observer.vue)

```vue
<template>
  <div class="observer"/>
</template>
<script>
export default {
  data: () => ({
    observer: null
  }),
  mounted () {
    const options = {
      rootMargin: '50px'
    }
    this.observer = new IntersectionObserver(([entry]) => {
      if (entry && entry.isIntersecting) {
        this.$emit('intersect')
      }
    }, options)
    this.observer.observe(this.$el)
  },
  destroyed () {
    this.observer.disconnect()
  }
}
</script>
```

调用模板：

```vue
<template>
  <div>
    <ul>
      <li class="list-item" v-for="item in items" :key="item.id">{{ item.name }}</li>
    </ul>
    <observer @intersect="intersected"/>
  </div>
</template>

<script>
import Observer from './Observer'
export default {
  name: 'Demo4',
  data: () => ({
    items: [],
    page: 1
  }),
  components: {
    Observer
  },
  methods: {
    async intersected () {
      const res = await fetch(`https://jsonplaceholder.typicode.com/comments?_page=${this.page}&_limit=30`)
      this.page++
      const items = await res.json()
      this.items = [...this.items, ...items]
    }
  }
}
</script>

<style scoped>
ul {
  padding: 0;
  margin: 0;
  list-style: none;
}
li {
  text-align: left;
  padding: 10px 20px;
  counter-increment: step-counter;
}
li::before {
  content: counter(step-counter);
  margin-right: 5px;
  font-size: 80%;
  background-color: rgb(200,200,200);
  color: white;
  font-weight: bold;
  padding: 3px 8px;
  border-radius: 3px;
}
</style>
```

#### 示例

{% jsfiddle dauwrqfc %}
