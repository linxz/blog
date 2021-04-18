---
layout: post
title: 轻松实现进度条的方法之 meter
tag: CSS基础
author: 林小志
---

先看来一张图片，一张带有进度条效果的图片，或者美化一下，你可以当做是充电效果的图。
![](img/2021-04/meter_01.gif)

这张图是继上篇文章《[这样写 step 步骤条就轻松了](easy-to-implement-step-bar)》而延伸出来的，在上一篇中提到使用 `progress` 标签来实现一个进度条的效果，并没有提供任何相对比较具象的 demo。这次，不仅有了一个比较具象的 demo（虽然不是特别炫酷），同时，还将会介绍一个新的标签，一个比 `progress` 更强大的标签。

### 波浪动画效果
这个效果其实就是两个标签元素叠加而成，为了形成那种所谓的波浪的效果，会对两个元素做细微的差异处理，以及在不同进度值显示不同的颜色值。

![](img/2021-04/meter_02.png)

![](img/2021-04/meter_03.png)

在这两张图中，我们能够发现不同的点是：
* 圆角的幅度各异；
* 两个元素的透明度不同；
* 两个元素似乎有错落感；
* 两张图的颜色不同；

考虑到希望是通过一个 HTML 元素标签来实现效果的，那么这里用到的波浪就用 `::before` 和 `::after` 了。用这两个也是有一些其他原因，因为我们有一个动画效果需要使用 `animation` 属性，但是在伪元素中，后面提到的几个伪元素在 Chrome 中不支持，不过在 Safari 中却是可以的。

#### 圆角的处理
这个圆角是修改了 `border-radius` 的八个值所得，具体是怎么样的一个值，完全是看心情，比如我是这样的。

![](img/2021-04/meter_04.png)

图中所示的小工具是很早之前折腾的一个处理圆角的小工具，用这个小工具就是可以直接拖拉每个角，然后得到效果，就不用一个值一个值的手动修改了。

#### 透明的处理
直接使用不同的 `opacity` 值，叠加一下就好了。

#### 错落感
结合 `opacity` 属性，再通过 `left` 或者 `margin-left` 等值进行位置偏移，最终结合不同的 `border-radius` 就可以了。

#### 不同颜色
这个就是要结合我们接下来要提到的 HTML 元素了，该元素可以让我们在不同的 `value` 值展示不同的颜色效果。相比较 `progress` 元素，会有更多的可配置性。

![](img/2021-04/meter_05.png)

### meter 标签
这个就是我们要了解的 `meter` 标签，相比较 `progress` 而言，多了几个阶段标识，从而也就是有了不同的颜色值。

同样的 `meter` 标签在打开 **shdow DOM** 查看的时候，也是可看到几个伪元素的存在，相比较 `progress` 而言，我们可以发现，同样是三层嵌套的结构，但是在 `meter` 的最后一层嵌套时，伪元素的类名是会根据 `value` 的情况而改变的。正因为如此，才会有不同阶段的颜色值。

#### 常用属性列表
* min
* max
* value
* low
* high
* optimum

相比 `progress` 多了 `low`、`high` 以及 `optimum` 这三个可控制进度展示效果的属性。不同的取值结果最终将直接影响颜色显示效果。正常情况下，一般建议是：**min ≤ low ≤ high ≤ optimum ≤ max**，是的，只是说正常情况下，如果非正常情况下，一定要让 `high` 小于 `low` 也不是不可以，只不过最终颜色的表示就呵呵了。如果 `optimum` 是在 **min** 和 **low** 的区间之间，那么可能导致的结果就是两种颜色互调。

#### 伪元素列表
* ::-webkit-meter-inner-element
* ::-webkit-meter-bar

上述这两个跟 `progress` 中的没啥区别，就是层级嵌套，包裹着用的。而下面这三个就是有差异了。

* ::-webkit-meter-optimum-value
* ::-webkit-meter-even-less-good-value
* ::-webkit-meter-suboptimum-value

从命名可以看到，主要就是三种不同值的颜色表示方式。

* even-less-good（红色）
* suboptimum（黄色）
* optimum（绿色）

![](img/2021-04/meter_05.png)

### 样式优化

```html
<meter min="0" max="10" low="3" high="6" optimum="7" value="2"></meter>
```

简单清晰明了的 HTML 代码，后面我们需要改变就是 `value` 值就可以了。

```css
meter {
  position: relative;
  width: 200px;
  height: 50px;
  margin-top: 100px;
  border: 5px solid #000;
  border-radius: 16px;
  overflow: hidden;
  appearance: none;
}
```

这部分关键的主要就是 `position`、`overflow` 以及 `appearance` 这三个，`appearance` 主要作用是为了修改外观样式，而另外两个则是在下面控制 `::before` 和 `::after` 的时候所使用的。

```css
meter::after,
meter::before {
  content: '';
  position: absolute;
  top: -200%;
  left: var(--roundLeft);
  width: 200px;
  height: 200px;
  border-radius: 182px 171px 150px 202px / 200px 219px 141px 149px;
  background-color: var(--bgc);
  opacity: 0.3;
  z-index: 1;
  animation: 3s linear 0s infinite rotateItem;
}
meter::before {
  left: calc(var(--roundLeft) - 5%);
  border-radius: 151px 132px 178px 118px / 174px 129px 189px 172px;
  opacity: 0.5;
}
```

在这里可以看到，有一个 `animation`，所以，还需要补上一个 `@keyframes` 的关键帧。

```css
@keyframes rotateItem {
  from {
    transform: rotate(1deg);
  }

  to {
    transform: rotate(360deg);
  }
}
```

也是很简单的一段动画样式，就是为了让那个不规则的圆圈圈转起来。

心细的你你肯定还看到了 `--roundLeft` 和 `--bgc` 这两个 CSS 变量，以及在 `::before` 中使用 `calc()` 计算偏移量的方式。这两个 CSS 变量的主要作用是后面为了通过属性选择符去控制每个阶段的颜色和位置，比如这样：

```css
meter[value="0"]::after,
meter[value="0"]::before {
  --roundLeft: -90%;
  --bgc: #f00;
}
/* 省略部分 */
meter[value="5"]::after,
meter[value="5"]::before {
  --roundLeft: -50%;
  --bgc: #f0f;
}
/* 省略部分 */
meter[value="10"]::after,
meter[value="10"]::before {
  --roundLeft: 0%;
  --bgc: #0f0;
}
```

前面我们提到了，是可以根据 `value` 值，直接通过三个不同的伪元素去改变颜色。如果我们不是使用 `::before` 或者 `::after` 的话，的确是可以的。但是我们现在是使用了，因此那三个颜色值就没什么作用了，也无法从这样的层级结构中传递到外层作为继承使用。

![](img/2021-04/meter_06.png)

所以，干脆就直接把这三个颜色值透明化。

```css
meter::-webkit-meter-bar,
meter::-webkit-meter-optimum-value,
meter::-webkit-meter-even-less-good-value,
meter::-webkit-meter-suboptimum-value {
  background-color: transparent;
}
```

这么一折腾，最终我们其实并没有利用 `meter` 组件特性，反而只是结合 `value` 属性，通过属性选择符而实现了位置的变化。但是这几个伪元素始终是存在的，只不过这个 demo 无法证明其可用性。或许是我过于期望想通过每个值得到效果，并且又想要加上 `animation` 动画效果的关系。

假设，如果 Chrome 中能够很好支持 `animation` 的动画效果，就可以不用利用 `::before` 和 `::after` 来实现，也就有可能通过 **Shadow DOM** 中的伪元素了。

### 最后的效果
在最后，`meter` 标签的主要作用是展示，而如果希望有互动效果的话，那么我们可以做的大概可能就有：
* 通过 N 个 `:hover` 伪类去实现鼠标滑过改变的效果；
* 通过 `:checked` 实现点击后切换不同颜色的效果；
* 外部通过修改 `input` 输入改变 `meter` 的 `value` 值，而这里我们使用了 `type='range'` 的 `input` 来实现滑动拖拉改变值；

```html
<input type='range' value="2" step="1" min="0" max="10" id="changeMeter" />
<script>
  const mv = document.getElementById('meterValue');
  const cm = document.getElementById('changeMeter');
  cm.onchange = function () {
    mv.value = cm.value;
  }
</script>
```

![](img/2021-04/meter_07.png)

最后通过样式，覆盖在 `meter` 上，同时隐藏这个 `input` 就好了。

```css
#changeMeter {
  width: 200px;
  height: 50px;
  position: absolute;
  top: 100px;
  left: 0;
  z-index: 5;
  opacity: 0;
}
```