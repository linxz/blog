---
layout: post
title: 这样写 step 步骤条就轻松了
tag: CSS基础
author: 林小志
---

相信很多朋友在写页面的时候都会遇到类似步骤条的效果，当进入到第 x 步的时候，前面是高亮的，后面是灰的，甚至还有可能是其他的一些样式，反正基本上就类似这样的一个效果。

![步骤条的粗略效果](img/2021-04/bg_step.png)

对于这样的效果，一般情况下都会用一个类似 `width: 33%;` 这样的宽度或者位置控制，写在 `style` 属性中，并且都是通过 JS 去计算的。其实，在 HTML5 的表单类型属性中，我们可以结合伪元素快速实现类似步骤条的效果，而且仅仅只是需要修改该标签的 `value` 属性值即可。

现在要说的是 `progress` 标签元素，从代码上来看，十分简单，在标签元素中间存在一个与 `value` 值相同的内容，主要是考虑如果低端浏览不支持的话，还是展示一下这个数字结果，而我们现在则是考虑浏览器兼容的情况下所做的事情。如果比较在意兼容性的话，可以查看一下 [https://caniuse.com/progress](https://caniuse.com/progress)。

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

在前面那张带有伪元素的图中，我们可以看到这个 `progress` 中还包含了以下三个伪元素：
* -webkit-progress-inner-element
* -webkit-progress-bar
* -webkit-progress-value

然后再加上 `::after` 和 `::before` 这两个，那么一共有 5 个元素可用，当然，这 5 个伪元素是有层级关系的。

`inner-element > (bar > value) + before + after` value 包含在 bar 中，bar 又在 element 中，最后 element 与 before 和 after 同级。

说分析，其实也没啥可分析的，知道了这几个伪元素的关系，除了 `before` 和 `after` 需要加 `content` 属性以外，其他三个在具体运用时就跟普通标签元素没任何区别。

### 特别注意事项
知道了这几个伪元素存在后，是不是有种很幸福的感觉，一个 `progress` 居然包含了这么多伪元素，就跟一个组件似的，哦，对，这本来就是一个“组件”。所以接下来我们是不是就马上可以开始吭哧吭哧地写样式，做效果呢？

```css
progress::before {
  content: 'linxz开始样式处理';
}
progress::after {
  content: '简单粗暴的样式linxz';
}
progress::-webkit-progress-inner-element {
  box-sizing: content-box;
  border: 3px dashed #333;
}
progress::-webkit-progress-bar {
  background: #0f0;
}
progress::-webkit-progress-value {
  background: #f00;
}
```

打开页面看一下，是不是发现哪里不对呢？

![错误的示例](img/2021-04/progress_03.png)

这肯定不对啊，不是设置了绿色的 bar，红的 value 吗？可是为什么没效果呢？还有 element 的边框也没出来，这是为什么呢？

其实这主要是因为我们还少写了一个属性，需要在 `progress` 中加入这个。

```css
progress {
  -webkit-appearance: none;
}
```

现在我们再看看会发现，这个世界开始变了。

![正确的示例](img/2021-04/progress_04.png)

#### appearance
该属性的主要作用是改变元素的外观，而 `progress` 内置了一个外观样式，默认值是 `auto`，也就代表着这个元素将很有可能会跟着系统的主题而变化。在 MDN 有这么一句介绍：

> The appearance CSS property is used to display an element using platform-native styling, based on the operating system's theme.

同时必不可少的会附带上了很多属性值的效果，在这里就不再一一展开说明了。但需要注意，如果我们要对一些特殊的组件，尤其是表单类型的，在不在是否有默认的外观属性，就随手加一下。当然啦，最稳妥的方式就是通过 DevTools 看一下 computed 后的样式就知道了。

![computed 的样式](img/2021-04/progress_05.png)

### 小结一下

首先请注意，文中并未相对应的属性进行深入说明，比如 `value`、`min` 以及 `max`，这些也没啥好说明的；其次呢，主要是想跟大家提一下 `progress` 中我们可以利用 `value` 等属性更快捷地获取到一个进度条的值，然后结合 `progress` 的伪元素做一些页面效果。

是的，主要想说的就是这么一点。

可能有人会觉得 demo 的效果很丑，那是必然的。手头上没有最直接的 demo，只是突然有了这么一个想法，所以才写了这么一篇简单介绍的内容。但是请一定要相信，这里有 1 个 `progress` 和 5 个伪元素可以用，相当于 6 个标签元素，可以做的事情还是不少。比如加上渐变、动画什么的，好看的效果就有了。

但必须要注意，不能遗忘 `appearance` 这个属性！