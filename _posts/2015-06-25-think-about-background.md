---
layout: md
title: 回味background
category: CSS属性基础
author: 林小志_linxz
---

# 回味background

`background`是在CSS中使用率很高的一个属性之一，由最初的几个简单的属性到现在新增了很多的属性，比如`background-size`、`background-origin`以及`background-clip`等属性，还可以设置**多背景**来达到更多的炫酷效果。

> 以下要谈的东西请不要在**IE6**中查看，只是在一些版本比较新的浏览器中玩耍娱乐，虽然有些代码在**IE6**中也是有效的。

## background的各个属性

其实`background`是一个复合属性，这个大家都知道，因为这个属性中包含了很多属性，比如：

* `background-color`
* `background-image`
* `background-position`
* `background-attachment`
* `background-repeat`

> 应该没有遗漏吧……基本上都是直接写`background`属性，单独分开写总是令我自己提心吊胆的，感觉好像遗漏了什么。

然后随着**CSS3**这个强大的东西到来之后，又新增了几个属性：

* `background-size`
* `background-origin`
* `background-clip`

这里没有多背景的属性*（Multiple Background Images）*，因为多背景的属性其实就是用一个半角的逗号`,`隔开而已。

### background-color

这个属性大家都知道，写CSS肯定都知道，不知道的话，我觉得就肯定没写过CSS了。不知道我这样说大家认同不认同，我觉得挺对的。对于`background-color`这个属性，用起来很简单，跟多背景图没有任何关系，仅仅只要知道颜色值在CSS中是怎么表示的就可以了，比如十六进制的方式`#F32600`、或者用RGB的方式`rgb(200,200,125)`，当然还有hsl之类的方式，反正只要知道，那肯定就能得到这样一个背景色的写法：

`background-color: #F32600;`

好吧，废话扯了一堆，其实我想说的是，这个`background-color`的显示区域是跟`background-clip`有关的，请看图：

![background-color 效果图](/blog/img/2015-06/demo_01.png)

demo: [http://jsbin.com/fiyazayuho/2/](http://jsbin.com/fiyazayuho/2/)

	div {float: left;width: 200px;height: 60px;border: 5px dashed #f00;margin:0 20px 20px 0;padding:30px;background-color: #ff0;}
	div + div {background-clip: border-box;}
	div + div + div {background-clip: padding-box;}
	div + div + div + div {background-clip: content-box;}

### background-image

背景色看过了，那么就是看看背景图片的引用，只要在`background-image`中，添加`url`地址就可以，比如：`background-image: url()`，这个括号里的地址可以是**相对路径**也可以是**绝对路径**，反正只要是有效路径就可以，但建议不要用中文/全角字符，如果有空格的话，那么就建议个路径加上引号，当然如果没有这些特殊的命名方式，或许就更好了。

就目前为止，`background-image`还没有到好玩的地步，只要知道`background-image`会盖在`background-color`上面，然后如果在`url()`后面写半角英文逗号的话，就可以玩多背景图了，比如：`background-image: url(name1.jpg), url(name2.png), url(name3.gif);`。

效果就好像这样：

![background-image和background-color的关系](/blog/img/2015-06/demo_02.png)

然后如果我们简单玩一下多背景图片的话，那就是这样了：

![multiple background image](/blog/img/2015-06/demo_03.png)

demo: [http://jsbin.com/lusuce/1/](http://jsbin.com/lusuce/1/)

	div {width: 100px;height: 50px;padding: 50px;
		background-color:#ccc;
		background-image:url(http://sfault-avatar.b0.upaiyun.com/166/281/166281916-1140000000145114_big64);
		background-repeat: no-repeat;
	}

	div ~ div {
		background-image: url(http://sfault-avatar.b0.upaiyun.com/604/519/6045191-1140000000383699_big64), url(http://sfault-avatar.b0.upaiyun.com/166/281/166281916-1140000000145114_big64), url(http://sfault-avatar.b0.upaiyun.com/393/562/3935623170-1140000000142909_big64), url(http://sfault-avatar.b0.upaiyun.com/287/570/2875704635-1140000000145753_big64);
		background-position:top left, top right, bottom left, bottom right;
	}
	
### background-position

背景定位这个东西写CSS的朋友肯定都知道怎么用，不然就玩不了**CSS Sprite**，不过大家一般用的时候，属性值都是`top`、`left`、`center`、`bottom`等组合，或者要么就是百分值和px值这几种，对于这几种大家都已经很属性了，也没什么好说的，都是相对于容器的左上角来计算的。

不过这里需要提一点的就是，`background-position`的位置是跟`background-origin`相关的，并且如果设置了`background-clip`的话，那么效果又不一样了，感觉上这几个的关联性还不小哦。具体的效果等一下我们看了`background-origin`和`background-clip`的属性值之后，就会明白了。

### background-origin

`background-origin`的作用是用于处理背景定位的原点坐标位置，共有三个属性值：

* `border-box`
* `padding-box`
* `content-box`

这三个属性值，如果了解盒模型的同学肯定就明白了，分别就是针对**边框**、**内边距**、**内容区域**三个部分的左上角为坐标。借用w3c官网上的一张图片来简单温习一下盒模型。

![盒模型](http://dev.w3.org/csswg/css-backgrounds/box)

从这个盒模型中可以看到，如果我们对一个容器设置了**边框**或者**内边距**的时候，那么背景的原点位置肯定就是会受到影响了。那么我们结合前面的`background-position`来做一个简单的效果：

![改变background-orgin后的效果](/blog/img/2015-06/demo_04.png)

demo: [http://jsbin.com/hitoji/1/](http://jsbin.com/hitoji/1/)

	div {width: 50px;height: 50px;border: 10px dashed #f00;padding:20px;
		background-image: url(http://sfault-avatar.b0.upaiyun.com/196/388/1963885910-54af96065b546_big64);
		background-repeat: no-repeat;
		background-origin: border-box;
	}
	div ~ div {background-origin: padding-box;}
	div ~ div ~ div {background-origin: content-box;}
	
从这里可以看到三个值的改变之后，效果背景图片的所在位置就完全不一样了，不过在背景中还有一个新增的属性，再加上去后，会发现更不一样的效果。

### background-clip

背景修剪的位置，这个也是根据`background-origin`属性值来改变效果的，那么我们基于上面的那个demo，再增加点东西看看效果。这里要提一下的是，`background-clip`的属性值跟`background-origin`是一样的，都是：

* `border-box`
* `padding-box`
* `content-box`

![改变background-clip的效果](/blog/img/2015-06/demo_05.gif)

### background-attachment

这个属性基本上来说，现在大家似乎都不会去关注，个人感觉好像是这样的。不过不管是不是这样，反正我知道我个人真的没去关注过，而且经常会搞不灵清`background-attachment`的属性值区别。基本上的作用就是定义一张不重复的图片随着内容区域的滚动而滚动，好吧，我感觉自己又把概念混乱了。

先看这个属性有三个属性值：

* `fixed`
* `local`
* `scroll`

然后从分别看他们的区别吧，这样应该就能让我自己清楚了，或许也能让你们在看这里的时候也清楚了。

	body {height: 1000px;}
	div {width: 300px;height: 300px;border: 1px solid #f00;
		background-image:url(http://sfault-avatar.b0.upaiyun.com/286/347/2863479281-1030000000341132_huge256);
		background-repeat: no-repeat;
		background-attachment: scroll; /* 会在这里改变属性值 */
	}

首先是默认的属性值`scroll`：

![background-attachment值为scroll时](/blog/img/2015-06/demo_06.gif)

那么这个时候，我们把`scroll`的值修改为`fixed`后再看看效果：

![background-attachment值为fixed时](/blog/img/2015-06/demo_07.gif)

很明显看到了对比，好吧，这个时候我自己也清楚了这两个属性的区别了，不过这两个属性值现在是在`div`标签里的，如果放在`body`标签里又会怎么样呢，我想效果应该也是会有区别的，有兴趣想试试的可以尝试一下。

那么现在来看第三个新的属性值：`local`这玩意，看这个的话，我们就在`body`标签里也增加一个背景图片，做一个对比看看效果。

[http://jsbin.com/cenipe/1/](http://jsbin.com/cenipe/1/)

不知道看的效果是不是这样呢？

![background-attachment的几个属性值的变化效果](/blog/img/2015-06/demo_08.gif)

### background-repeat

`background-repeat`这个也是大家最常用的一个背景属性了，对于这个属性，我们一般也就是用到`repeat`、`no-repeat`、`repeat-x`和`repeat-y`这么四种，对于这四种，其实大家也都是很好理解的，无非就是**各个方向都重复**、**都不重复**、**X轴重复**和**Y轴重复**，然后结合**盒模型**的原理，就可以猜想到是从容器的原点位置，以X轴或者Y轴进行重复平铺背景图片，如果一张图片在容器中没有完全显示的话。

其实吧，就算背景图片的宽高比容器的宽高要大，我觉得背景图片一样还是在平铺着的，只是超出了容器的范围，看不到了而已。

对于这四个属性都是常用的，大家都知道，也没啥说的，不过，新增的两个属性不知道各位知道不，我觉得挺好玩的，分别是：

* `space`
* `round`

这两个属性使用起来，背景图片就灵活很多了。

#### background-repeat: space;

根据背景图片的大小，然后通过计算容器的大小来平铺背景，多余的部分用空白来填充，这样的平铺方式是不会出现图片被截成一半的情况，如图：

![background-repeat: space的效果](/blog/img/2015-06/demo_09.png)

具体的效果，大家可以看看这个demo：[http://jsbin.com/wigaxi/1/](http://jsbin.com/wigaxi/1/)

#### background-repeat: round;

根据背景图片的大小，然后通过计算容器的大小，压缩图片来填充容器，这样的平铺方式也是不会出现图片截成一半的情况，不过跟`background-repeat: space;`不同的是这种填充方式不会出现空白的区域，而是会去压缩图片的大小来适应容器，如图：

![background-repeat: round的效果](/blog/img/2015-06/demo_10.png)

具体的效果，大家还是可以直接看看demo效果：[http://jsbin.com/wefuwa/1/](http://jsbin.com/wefuwa/1/)

#### 影响background-repeat的属性

影响`background-repeat`的属性无非也就是`background-origin`这个属性了，这个属性直接改变了背景在一个容器中的计算显示方式，就跟`box-sizing`一样，直接影响到了**盒模型**的表现方式，具体的也就不做demo了，有兴趣的可以增加这个属性尝试一下，会发现原来不会出现被截断的背景图片，似乎出现了截断的显示效果了。

![background-origin对background-repeat的影响](/blog/img/2015-06/demo_11.png)

### background-size

刚在前面提到了`box-sizing`属性，那么在背景中也有一个*size*相关的属性，就是`background-size`，但这个并不是去改变**盒模型**的属性，而是让背景图片按照一定的比列去适应容器的一个属性。简单来说，这个属性就是会拉伸、缩小背景图片大小的，将以往不能改变背景图片大小的惯例打破了。

先来看一个示例：

![background-size的简单示例](/blog/img/2015-06/demo_12.gif)

在这个示例中我们可以看到，当没有`background-size`的时候，一张背景图片是很老实得呆在左上角，并且是保持着原来的大小，当然，有了这个属性，它还是呆在左上角，不同的是，这张背景图片的尺寸改变了：

* 100%： 一个值的时候，其实就是把宽高都拉伸到相对于容器的宽度，简单来说，撑满了；
* 50%： 这个当然就是宽高都只有容器的一半（50%）的时候；
* 100% 50%： 这个呢就是宽度相对容器是100%的，而高度是50%；

从上面三个情况来看，大家应该已经很清楚的明白了`background-size`的值是针对容器的，并且是先宽度再高度。

那么我们现在用的是`%`这个百分比的相对单位，其实我们也是可以用`px`来表示啊，或者是`em`，当然也可以是现在很多人喜欢用的`rem`作为单位值来使用。

如果不想用这些单位来计算的话，那么其实还有一个方式，就是用下面这三种值：

* `auto`
* `contain`
* `cover`

这三种属性值中`auto`没什么说的，大家都懂，该是怎么样的就是怎么样，不同的是后面两种，会去强制将背景图片做一些改变。

#### background-size: contain;

等比缩放，以最大的尺寸填充到容器中，但不一定会撑满容器，而出现背景图片显示不全的情况：

![background-size:contain的表现](/blog/img/2015-06/demo_13.gif)

#### background-size: cover;

等比缩放，以最小的尺寸填充到容器中，一定会撑满容器，且可能会出现背景图片显示不全的情况：

![background-size:cover的表现](/blog/img/2015-06/demo_14.gif)

#### 相关讨论

* 对background-size的建议: [https://github.com/linxz/blog/issues/1](https://github.com/linxz/blog/issues/1)

## 想想background也真神奇

一个简单的`background`属性，现在折腾出来的效果也真的是多啊，如果把各种属性结合在一起，估计出现什么效果有时候自己都想象不到，尤其是新增的几个属性，特别是`background-origin`和`background-size`，改变了曾经对`background`的一些看法。

然后结合多背景图片的话，那将又是一个神奇的世界，关于多背景和`background`的缩写，也没什么好说的了，一笔带过吧。

### 多背景图片

用英文半角逗号隔开，如果要用简写模式的话，那么就先写完一个`background`简写属性后，再用`,`半角逗号隔开，继续写，如：

	background: url(a.png) top left no-repeat,
                url(b.png) center / 100% 100% no-repeat,
                url(c.png) white;

### background 简写模式

以前没有那么多新增属性的时候，简写就是：

	background: url(a.png) top left no-repeat fixed #000;
	
现在有了之后，就稍微改变了一下，不过也相差不多：

	background: url(a.png) top left / cover no-repeat fixed content-box content-box #000
	
格式就是：
	 
	 <bg-image> || <position> [ / <bg-size> ]? || <repeat-style> || <attachment> || <box> || <box> || <'background-color'>	
	 
## 最后想说

`background`的规则是这样，但是最终如果多个属性结合在一起，真不知道会变成什么样。看着是一个个很简单的属性，但是多个在一起，或许会让背景更漂亮，或许也会让你的背景变得很难看……
