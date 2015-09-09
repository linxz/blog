---
layout: md
title: 闲扯 background-position 的单位属性值
category: CSS属性基础
author: 林小志_linxz
---

# 闲扯 background-position 的单位属性值

其实对于 `background-position` 这个 **CSS** 属性有多少个属性值，大家一查就知道就知道了，这里我想说的是今天扯这个属性值，是因为自己在无意间看到有朋友在阅读了[《CSS那些事儿》之后的一个读后感](http://www.cnblogs.com/langjt/p/4281477.html)中提到了一点：

> 属性值为百分比时，将以图片的 **中心点** 为基准计算其相对位置，而使用px像素值时将以图片的 **左上角** 为基准。

然后我自己开始怀疑这个观点，虽然曾经是我提到的，但毕竟是09年出版的书，而且自己一直都是那么稚嫩与无知，就在水群里扯了一下，最后先折腾了 [http://linxz.github.io/tianyizone/test_background-position.html](http://linxz.github.io/tianyizone/test_background-position.html) 这个做一些测试以及讨论的基础。

讨论的过程主要是针对三种属性值做了讨论：

* 长度单位中的 `px` 单位值；
* 特殊字段值（`top`、`bottom`、`left`、`right`、`center`）；
* 百分比 `%`；
* 长度单位中的 `em` 单位值（临时想到的……）；

## 长度单位中的 `px` 单位值

对于 `px` 这个单位值，大家都不陌生，一直在用，也是很好理解的，没什么好说的，在不改变原点的前提下，背景图片的定位是通过图片的左上角开始计算与容器左上角的距离，具体的可以通过 [http://linxz.github.io/tianyizone/test_background-position.html](http://linxz.github.io/tianyizone/test_background-position.html) 这个来看效果，随便点一个格子看看就知道了。

![px值改变的效果](/blog/img/2015-09/background-position_px_value.gif)

格子都是 `50x50` 大小的，外面的容器是 `500x500`，这样选择数值是为了方便计算，也比较直观。

## 特殊字段值

这个也是比较容易理解的一个属性值，也很直观，只要x轴和y轴的两个值都出现的时候，就是在容器中去寻找相对应的位置来定位，但这个值却不是根据 `px` 这样来计算的，而是通过百分比的方式来计算的，下面这段是来自 [http://www.w3.org/TR/REC-CSS2/colors.html#propdef-background-position](http://www.w3.org/TR/REC-CSS2/colors.html#propdef-background-position)：

> * **top left and left top** Same as '0% 0%'.
* **top, top center, and center top** Same as '50% 0%'.
* **right top and top right** Same as '100% 0%'.
* **left, left center, and center left** Same as '0% 50%'.
* **center and center center** Same as '50% 50%'.
* **right, right center, and center right** Same as '100% 50%'.
* **bottom left and left bottom** Same as '0% 100%'.
* **bottom, bottom center, and center bottom** Same as '50% 100%'.
* **bottom right and right bottom** Same as '100% 100%'.

那么这里提到了百分比的一个属性值，这个等一下我们会说一下，就目前的情况来看，百分比的计算方式还是稍微有一点特殊的，具体的效果呢还是可以在 [http://linxz.github.io/tianyizone/test_background-position.html](http://linxz.github.io/tianyizone/test_background-position.html) 这里来直接看看效果：

![特殊字段值改变后的背景坐标位置](/blog/img/2015-09/background-posiiton_special_value.gif)

## 百分比 `%`

终于到百分比这个属性值的时候了，对于这个值才是我们在水群里争论的一个主要内容，到底这个百分比的计算是根据什么元素的哪个位置来计算的呢。在 [http://www.w3.org/TR/REC-CSS2/colors.html#propdef-background-position](http://www.w3.org/TR/REC-CSS2/colors.html#propdef-background-position) 是这样说的：

> With a value pair of '0% 0%', the upper left corner of the image is aligned with the upper left corner of the box's padding edge. A value pair of '100% 100%' places the lower right corner of the image in the lower right corner of padding area. With a value pair of '14% 84%', the point 14% across and 84% down the image is to be placed at the point 14% across and 84% down the padding area.

好吧，其实我英文真的很烂，要翻译这段内容的话，就要打开翻译软件什么的了，然后翻译后我也不一定能翻译正确，大概感觉上比较重要的是这句话。

> With a value pair of '14% 84%', the point 14% across and 84% down the image is to be placed at the point 14% across and 84% down the padding area.

似乎大概意思是：**如果是14% 84%的这个值，那么就以图片的14% 84%的坐标点，放置在容器的14% 84%的位置。**那这样理解的话，就是说明，如果是用百分比来作为 `background-position` 的属性值的话，那么背景图片相对于容器的中心点是随时都在改变的。

那么我们还是以 [http://linxz.github.io/tianyizone/test_background-position.html](http://linxz.github.io/tianyizone/test_background-position.html) 这个来效果展示，这次我们选值为 `background-position: 10% 0`，也就是说x轴的值是`10%`，y轴是`0`，最后的效果就是这样：

![background-position: 10% 0 时的效果](/blog/img/2015-09/background-position_percent_01.png)

前面说过，这 demo 中的格子是 `50x50` 来计算的，那么 `10% * 50` 的话，就是 `5px` 了，我们放大超过的那部分来看看是不是 `5px`。

![background-position: 10% 0 时的放大效果](/blog/img/2015-09/background-position_percent_02.jpg)

看来初步假设是成功了，那么为了再次证实一下，我们把y轴的值改成`20%`看看效果。

![background-position: 10% 20% 时的效果](/blog/img/2015-09/background-position_percent_03.png)

如果说还是以左上角为原点来计算位置的，我们这个时候应该是觉得背景图片的位置是 `50px 100px` 的位置（`10% * 500px` `20% * 500px`），但事实上却不是这样的，从上面这张图以及前面我们提到的那个 `5px` 的偏差，以及结合 w3c 官网文档中提到的，还要去用这个百分比的值计算背景图的坐标，也就是背景图的 `5px 10px`（`10% * 50px` `20% * 50px`）这个点是坐标点，所以，我们再放大截图可以看到效果是这样的：

![background-position: 10% 20% 时的效果](/blog/img/2015-09/background-position_percent_04.png)

那么同理，如果我们用其他的百分比来计算的话，也都是一样的会相对应的改变背景图的坐标点，然后去定位这张背景图片在容器中的位置。

![以百分比的形式改变background-position的值](/blog/img/2015-09/background-position_percent_05.gif)

### 当时的理解是……

当时没看到 w3c 上的文档资料时在群中讨论的结果是，还是以背景图的左上角为坐标点，然后通过类似`margin负值`的形式来偏移图片的位置。假设 `background-position: 10% 20%` 的话，那么先以左上角为坐标点，图片应该是 `50px 100px` 的位置（`10% * 500px` `20% * 500px`），然后再计算图片自身的偏移量 `-5px -10px`（`10% * 50px` `20% * 50px`），从x轴和y轴各减去相对应的值，就好像是 `margin-left: -5px;` 和 `margin-top: -10px;` 的结合。

### 百分比这个值的小结

虽然当时讨论时的结论有点成果，但看了 w3c 文档资料后，发现其实还是没理解原来是同时在改变背景图片自身的坐标点。那么大家是怎么理解的呢？或许怎么理解都不重要，重要的是我们已经大概明白了百分比这个数值的一些特点。

## 长度单位中的 `em` 单位值

这个 `em` 单位值是在打算写之前突然想到的，对于 `em` 没了解过，但似乎是跟 `font-size` 有关联的，当我们使用 `background-position: 1em 2em` 这种方式时，再去改变父级的 `font-size` 就会看到背景图的位置是在变化的，不过有一点可以方向的就是，**背景图片的坐标是左上角**。

> **小小的提醒：** chrome 中小于 `12px` 字体大小的问题请不要忘记了，当 `font-size` 的值**大于 0px** 且**小于 12px** 时都是以 `12px` 的字体大小来计算的。

## 当出现这些特殊情况时……

以上的情况都是比较正常的时候，我们在选择 `background-position` 值的时候把x轴和y轴都计算在内了，那么假如出现这些特殊情况的话，又会怎么样呢？

* 只有一个值的时候；
* 如有 `background-size` 的时候；
* 如有 `background-origin` 的时候；
* 如有 `background-clip` 的时候；
* `background-size`、`background-origin`以及`background-clip`多个出现的情况时；

后面几种情况是 `background` 属性中新增的几个属性值，可以参考之前整理的[回顾background](/blog/css属性基础/2015/06/think-about-background.html)。

### 只有一个值的时候

当 `background-position` 只有一个属性值的时候会是怎么样的呢，假如值是 `background-position: 100px;`，那么我们可以看到的结果就是这样了：

![background-position: 100px;时的背景图位置](/blog/img/2015-09/background-position_single_px.png)

可以看到y轴的坐标值是`50%`，而x轴则是我们所设置的那个值。那么如果我们用其他的单位值又将是怎么样的，我就不截图了，有兴趣的大家自己可以试试，不过我想试出来的结果应该都是y轴是`50%`。

当然了，如果你用`top`或者`bottom`这个很明确是改变y轴的值时，这个时候x轴的值就是`50%`了。

### 然后`background-size`、`background-origin`以及`background-clip`的情况

这个只是当时做 [http://linxz.github.io/tianyizone/test_background-position.html](http://linxz.github.io/tianyizone/test_background-position.html) 这个页面的时候以及在群里讨论的时候提到了一下，猜想可能会改变点什么，然后刚简单测试了一下，这几个值只是可能影响到了坐标点的计算。看来是多心了，其实这些只是从另一个方面去改变了背景图片的大小而已，或者简单来说只是影响了 `width` 和 `height`，并不会影响 `background-position` 的计算方式。

## 最后……

终于扯完了，说到底 `background-position` 的位置关键就两个：

* 背景图片的左上角为坐标点；
* 百分比时，坐标点会跟着百分比数值而改变；

感谢看完我的扯淡……