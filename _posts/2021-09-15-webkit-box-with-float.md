---
layout: post
title: -webkit-box 在 Safari 中出现的兼容性问题（bug？）
tag: CSS基础
author: 林小志
---

## 问题来源背景
这个是当时在『iCSS前端CSS交流讨论』群中看到的，当时转为朋友的提问是这样的：

![](/blog/img/2021-09/-webkit-box-1.jpeg)

看这个图，主要是有两点：多行截断和 tag 环绕。多行截断那就是用 `-webkit-box` 那种方式，tag 环绕呢那就是早期图文环绕的方式，用 `float` 就好了。

理想是丰满的，现实是骨感的。未经他人之苦，一切都是那么轻松。当我提到说使用 `float` 就可以实现后，群里有朋友反馈说在 Safari 和移动端会有问题。

事实上我的确也没在意过，于是事后尝试了一下，的确如此。

![](/blog/img/2021-09/-webkit-box-2.jpeg)

## 摸索前进
这是一个比较奇怪的现象，首先就我个人而言的确是没有见到过这样的情况，之前都没有这样类似的需求。

### 初次尝试
在没有查阅任何资料之前，直接做了一个最简单的 demo 看效果。

```html
<div class="text-overflow">
  <span class="tag">new</span>
  很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容
</div>
```

一个很简单的 HTML 结构和一份很简单的 CSS 样式。

```css
.text-overflow {
  width: 200px;
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  font-size: 13px;
  line-height: 1.425;
  background-color: pink;
}
.tag {
  float: right;
  padding: 0 2px;
  color: #fff;
  background-color: #f32600;
  border-radius: 2px;
}
```

最终的效果就是前面所看到的，在 Chrome 中很完美地得到期望结果，但是 Safari 中发现 tag 不见了，并且有一条空白存在。

按照常规想法，一般这种内容突然不见的，如果不是被截断了就是被覆盖了，或者渲染有问题。尝试性在 `.tag` 中增加了 `position: relative;` 看看效果，发现 tag 出现了，并且是独占一行。

![](/blog/img/2021-09/-webkit-box-3.jpeg)

### 设想
tag 独占一行，说明至少有类似 **block** 块级元素的特性存在，而 `float` 之后本身就是会将元素转为块级。想到这里，突然想到，`-webkit-box` 这个是早期版本的 flex 布局，那会不会就跟 `display: flex;` 这个一样，当有了 flex 容器之后，flex 元素就不再是 **BFC**、**IFC** 之类的，而是 **FFC** 呢？

如果真是这样的话，那么是不是就可以多嵌套一层，由最外层控制文本多行截断，最里层控制 tag 的浮动效果呢？于是改变一下 HTML 结构。

```html
<div class="box">
  <div class="text-overflow">
    <span class="tag">new</span>
    很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容很多文本内容
  </div>
</div>
```

在原有的基础上，增加一层，CSS 部分也改变一下，其实只要把选择符 `.text-overflow` 换成 `.box` 就可以了。

```css
.box {
  width: 200px;
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
}
.tag {
  float: right;
  padding: 0 2px;
  color: #fff;
  background-color: #f32600;
  border-radius: 2px;
}
```

那么最终的结果就是这样。也就是说的确是 `-webkit-box` 的影响，而且应该就是 **FFC** 的关系。至于浏览器中差异可能就是兼容性问题了。

![](/blog/img/2021-09/-webkit-box-4.jpeg)

### 查资料
既然 `-webkit-box` 是早期 flex 布局的一个版本，那么必定还有其他一些属性，比如以下几个：

* `box-pack`;
* `box-align`;
* `box-orient`; 

那么就按照这个思路，我们可以看看 flex 相关的资料文档。

在之前的 flex 教程文档（https://www.yuque.com/linxz/flex）有关 `display: flex;` 有过介绍，前面也提到了 **FFC**，那么具体的我们也可以从 w3c 文档中看到：

> A flex container establishes a new flex formatting context for its contents. This is the same as establishing a block formatting context, except that flex layout is used instead of block layout. For example, floats do not intrude into the flex container, and the flex container’s margins do not collapse with the margins of its contents. Flex containers form a containing block for their contents exactly like block containers do. [CSS21] The overflow property applies to flex containers.

以上内容来自： https://www.w3.org/TR/css-flexbox-1/#flex-formatting-context

其中 **A flex container establishes a new flex formatting context for its contents.** 和 **floats do not intrude into the flex container**，至少说明了在 flex 布局中，浮动是有“可能”是无效的。

### 我的结论
针对这个问题的处理方式，我的结论，是的，仅仅只是代表我个人的一个结论就是：多套一层，由外层控制元素多行截断，最里层去实现浮动环绕的效果，避开因为 flex 容器而导致子元素浮动失效的情况。
