---
layout: post
title: 这样写 step 步骤条就轻松了
tag: CSS基础
author: 林小志
---

相信很多朋友在写页面的时候都会遇到类似步骤条的效果，当进入到第 x 步的时候，前面是高亮的，后面是灰的，甚至还有可能是其他的一些样式，反正基本上就类似这样的一个效果。

![步骤条的粗略效果](img/2021-04/bg_step.png)

对于这样的效果，一般情况下都会用一个类似 `width: 33%;` 这样的宽度或者位置控制，写在 `style` 属性中，并且都是通过 JS 去计算的。其实，在 HTML5 的表单类型属性中，我们可以结合伪元素快速实现类似步骤条的效果，而且仅仅只是需要修改该标签的 `value` 属性值即可。


## progress

先来看一下这个 `progress` 标签元素，从代码上来看，十分简单，在标签元素中间存在一个与 `value` 值相同的内容，主要是考虑如果低端浏览不支持的话，还是展示一下这个数字结果，而我们现在则是考虑浏览器兼容的情况下所做的事情。如果比较在意兼容性的话，可以查看一下 [https://caniuse.com/progress](https://caniuse.com/progress)。

```html
<progress value="33" min="1" max="100">33</progress>
```
从上面这个代码中我们很容易就可以理解，要展示的进度情况是在 **1 到 100** 之间的 **33** 这个位置，或者我们也可以直接理解为 **33%** 部分。在 chrome 浏览器中默认的效果就是这样了。

![progress默认效果](img/2021-04/progress_01.png)

简单的 HTML 标签，直观的效果。那么可能就有人会问了，现在只有这么一个标签元素，而一般进度是有几种颜色搭配的，那么需要怎么处理呢？是的，这的确是一个问题，但也仅仅只是从表面上看到的一个问题，如果我们把 DevTools 设置中的 **Show user agent shadow DOM** 打开，然后再选中这个 `progress` 元素，就会有新世界出现了。

![开启 shadow DOM](img/2021-04/progress_02.png)

现在我们可以看到在 `progress` 中其实还存在几个元素，那么接下来我们就可以利用这些伪元素来帮我们实现更多效果了。

```css
progress {
  display: block;
  width: 300px;
  height: 35px;
  margin-bottom: 10px;
  -webkit-appearance: none;
}
progress::-webkit-progress-bar {
  background: url(bg_step.png) no-repeat 0 0;
}
progress::-webkit-progress-value {
  background: url(bg_step.png) no-repeat 0 -50px;
}
progress[value="2"]::-webkit-progress-value {
  background-position: 0 -100px;
}
progress[value="3"]::-webkit-progress-value {
  background-position: 0 -150px;
}
```

很粗暴的用一张雪碧图来处理了一下，图片就是前面的那张步骤条图片，只不过最终的效果就是根据不同的 `value` 值，展示高亮部分的步骤。如果要好好完整的搞，那么还需要做细节地处理。

### 伪元素分析




> 文中提到的仅只是对其中几个属性做了介绍，并未面面俱到，全部介绍。