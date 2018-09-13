---
layout: post
title:  "记录自己css 3D transform变换"
categories: css

---

本文主要是总结了笔者在学习3D transform所收获的经验，希望通过笔者认为合理的知识点安排，能够让大家少走弯路，本文并不涉及2D transform,想了解相关知识的读者可以google查阅相关资料。希望看完之后能够有所收获，不正确地方，也希望大家不吝指教。<!-- more -->

### 什么是透视

在刚接触透视这个词语的时候，可以说大脑是一片空白。到底透视是个什么概念？为什么给元素设置了perspective属性，元素就能表现出3D空间？这事还得从绘画说起。

#### 透视现象

有一种现象，由于我们从小就己经习惯了，也就从不觉得奇怪，那就是：同样规格的电线杆本来是一样高的，但在路上看起来，却近高远低；同样型号的汽车本应是一样大小的，但在路上看起来却近大远小；所有的茶杯、水桶底本来都是平的，但我们时常看到的却是不同的弧线；火车的两条路轨本来都是一样宽的, 但我们时常看到的却是两条路轨向前延伸时,会越来越窄乃至合拢成一点等。这种视觉现象就是透视现象，总结来说就是：

- 近大远小
- 近宽远窄
- 近实远虚

<img src="https://ws3.sinaimg.cn/large/006tNbRwgy1fv7367u8onj30aj07574p.jpg" width="500px"/>

我们都知道现实世界是3D的，通过光线的直线传播最后在视网膜呈现的是平面图像，但是这种图像却富有立体空间感，以至于不让我们撞到墙，掉到河里去。可以说人眼看到的画面都是透视图。而我们如果想要在平面的纸上，表现出这种变化，表现出如我们视觉所反映出的、富有立体空间感的景物，简单的办法就是：固定你的眼睛位置，隔着透明玻璃窗将你所见的景物的形状依样描在玻璃上，这描下来的图形就会较准确地反映这种现象，就存在着客观的透视规律。因此，“透视”的含义就是：透过（假设的）透明平面去观察景物，研究物体在立体空间中的形态变化，即“透而视之”，用这种观察方法，可以在只有两度空间的透明平面上录画出具有三度空间特征的景物图形。这个图形叫透视图，最早研究透视的画家，就是采用这种办法来做的，透过总结透视图中的规律得出绘画透视图科学的方法，我们称之为透视学。所以说只要你在二维平面采用透视学中的方法，画出满足透视学的图画。就能欺骗人的眼睛，让图画富有立体空间感。如下图所示

<img src="https://ws2.sinaimg.cn/large/006tNbRwgy1fv73ulj0adj30u40jqna7.jpg" width="500px"/>

#### 透视相关术语

在透视图中有很多专业的术语，了解他们可以让我们更加容易理解css 3D transform。

<img src="https://ws3.sinaimg.cn/large/006tNbRwgy1fv74apg3z0j30f30bc0ti.jpg" width="500px"/>

视点：就是画者眼睛的位置。

中视线：由视点作出的射向景物的任何一条直线均为视线，其中引向正前方的视线为中视线，中视线始终垂直于画面。我们可以把画面当成是屏幕，perspective属性值就是中视线的长度。

主点：中视线与视平线的交点（也叫心点）.

灭点（消失点 Vanish Point）：透视图中，灭点是一个很重要的概念。在透视图中相互平行的直线延长会相交于一点，这点就是灭点，也叫消失点（Vanish Point）。根绝透视图灭点的数量可以分为一点透视，两点透视，三点透视。perspective-origin就是定义灭点位置。

更多关于透视的基础概念，希望大家自行google毕竟我们的重点还是专注于前端。

### 屏幕坐标轴

在了解透视之前，首先要先了解坐标轴。3D变形与2D变形最大的不同就在于其参考的坐标轴不同。2D变形的坐标轴是平面的，只存在x轴和y轴，而3D变形的坐标轴则是x、y、z三条轴组成的立体空间，x轴正向、y轴正向、z轴正向分别朝向右、下和屏幕外。

<img src="https://ws3.sinaimg.cn/large/006tNbRwgy1fv76r2hawmj30m60i00sr.jpg" width="500px"/>

面向用户的是z轴，垂直方向是Y轴，水平是X轴。rotateX, rotateY, rotateZ三个方法就是将元素沿着相应的轴进行旋转。

### 3D transform属性各个击破

在了解了一些基本的概念之后我们开始真正进入3D transform的世界。3D变形涉及的属性主要是transform-origin、transform、transform-style、perspective、perspective-origin、backface-visibility。

#### perspective属性

```
Value:	none | <number>
Initial:	none
Inherited:	no
```

我并不想直接引用规范里的介绍来说明perspective属性。简单来说就是设置了perspective属性的元素，该元素就具有立体空间的表现能力。不过仅对该元素的子元素有效，元素本身并不具有立体空间表现能力.perspective值定义了中视线的长度，直观说法就是用户眼睛到屏幕的距离，官方说法就是z=0的平面和用户之间的距离，每一个3d元素z>0则越大，z<0则越小，3d效果的强烈程度随着perspective值得增大而减小，当perspective值为none，小于或等于0不存在 3d变形。

<p data-height="265" data-theme-id="dark" data-slug-hash="aaGwvM" data-default-tab="css,result" data-user="sponia-joker" data-pen-title="no-perspective" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/sponia-joker/pen/aaGwvM/">no-perspective</a> by joker (<a href="https://codepen.io/sponia-joker">@sponia-joker</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

建议在进行3d transform时候，采用上面的html结构。舞台元素，容器元素，变形元素这样可以让模型保持简单而且容易理解。在上面demo中，我们将子元素沿Y轴旋转了45deg，但是页面并没有表现出我们期待的效果，实际效果是变形元素的宽度缩小。这是因为我们并没有给父元素设置perspective属性，元素不具备立体空间的表现能力，最终表现的是元素沿Y轴旋转之后再屏幕上的投影，所以宽度会缩小。但我们打开注释后，就会是我们期待的效果。大家可以在codepen上手动实践下。

`perspective`属性有两种书写形式，一种用在舞台元素上（动画元素们的共同父辈元素）；第二种就是用在当前变形元素上，与transform的其他属性写在一起。如下所示

<p data-height="265" data-theme-id="dark" data-slug-hash="GXdEVO" data-default-tab="css,result" data-user="sponia-joker" data-pen-title="perspective-function" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/sponia-joker/pen/GXdEVO/">perspective-function</a> by joker (<a href="https://codepen.io/sponia-joker">@sponia-joker</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

最后的效果和在父元素上设置perspective属性是一模一样。但两个属性还是有很大区别，之所以上面效果是一样，是因为舞台上只有一个元素，如果有多个元素就会表现出差异。

<p data-height="265" data-theme-id="dark" data-slug-hash="yxjoRY" data-default-tab="css,result" data-user="sponia-joker" data-pen-title="perspective-vs-perspective-function" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/sponia-joker/pen/yxjoRY/">perspective-vs-perspective-function</a> by joker (<a href="https://codepen.io/sponia-joker">@sponia-joker</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

显然当具有多个元素时候，perspective和perspective()函数具有不同表现效果。主要是因为在父元素上设置perspective属性，所有子元素具有相同的视点，而配合transform使用的perspective函数每个元素都拥有各自不同的视点。可以理解成1个人看 vs 5个人看。更加不可思议的是但设置perspective为100px时,给父元素设置perspective属性的demo中，倒数第二个元素会消失，这里读者可以仔细思考下，如果想不明白可以评论留言。

总结来说就是perspective赋予了元素3d空间展示的能力，可以说：无透视，不成3D。更多关于perspective属性的说明，强烈建议读者去[规范](https://www.w3.org/TR/2009/WD-css3-3d-transforms-20090320/#perspective)和[MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/perspective)了解。

#### transform-style属性
```
value:flat | preserve-3d
Initial:flat
Inherited:no
```
Transform-style属性定义了嵌套元素如何在3D空间进行渲染，当设置为flat时，元素所有子元素都被渲染在元素所在的2D平面，因此，围绕X轴或Y轴旋转元素，将导致位于正或负Z位置的子元素出现在元素的平面上，而不是在元素的前面或后面。如果transform-style值为preserve-3d，那么这种扁平化不会被执行，子元素会在3D空间中保持自己的位置。
元素默认会保持扁平化，又因为该属性无法继承，因此为了在3D空间中保存元素的层次结构需要在层次结构中的每个祖先元素都设置transform-style值为preserve-3d.由于transform-style只会影响元素的子元素，所以层次结构中的叶子节点不需要设置。
一般我们都是设置transform-style属性值为preserve-3d，为了演示两者的不同，可以通过点击codepen中的按钮交替显示

<p data-height="265" data-theme-id="dark" data-slug-hash="OoZrGq" data-default-tab="css,result" data-user="sponia-joker" data-pen-title="transform-style" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/sponia-joker/pen/OoZrGq/">transform-style</a> by joker (<a href="https://codepen.io/sponia-joker">@sponia-joker</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>



#### perspective-origin属性

```
value:x-axis y-axis
Initial:50% 50%
Inherited:no
```

perspective-origin属性比较简单，他设置视点x,y轴坐标。简单来说就是决定了我们看实物的角度。更详细说明可以参考

[css-tricks](https://css-tricks.com/almanac/properties/p/perspective-origin/)有关该属性的介绍。

#### backface-visibility属性

```
value:visible | hidden
Initial:visible
Inherited:no
```

“backface-visibility”属性决定转换后的元素的“背面”是否对用户可见。就好比一枚硬币，当我们沿Y轴旋转180deg时候，此时是背面朝向我们，backface-visibility就是用来设置背面是否可见。backface-visibility经常用来实现一些翻转效果

<p data-height="265" data-theme-id="dark" data-slug-hash="RYyvYX" data-default-tab="css,result" data-user="sponia-joker" data-pen-title="perspect-visibility" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/sponia-joker/pen/RYyvYX/">perspect-visibility</a> by joker (<a href="https://codepen.io/sponia-joker">@sponia-joker</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### 参考资料

1. https://www.w3.org/TR/2009/WD-css3-3d-transforms-20090320/#perspective
2. https://3dtransforms.desandro.com/perspective
3. https://www.w3cplus.com/css3/transform-basic-property.html
4. https://www.cnblogs.com/xiaohuochai/p/5351477.html
5. https://www.zhangxinxu.com/wordpress/2012/09/css3-3d-transform-perspective-animate-transition/#comment
6. https://css-tricks.com/almanac/properties/t/transform-style/
7. https://css-tricks.com/almanac/properties/p/perspective/
8. http://blog.sina.com.cn/s/blog_5cd887c70100avt0.html