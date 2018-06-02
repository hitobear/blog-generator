---
title: flex入门—了解这些flex属性
---

## 传统的页面布局

&emsp;&emsp;在flex出现之前，双列布局，三列布局，动态盒居中,绝对居中布局等常见的布局均是采用dispaly+float+定位来布局的，一般包括以下几种布局策略：

1. normal flow(文档流:块级元素从上到下，行内元素从左到右)
2. float + clear
3. position relative + absolute
4. display inline-block
5. 负margin(扩大宽度，产生位移，如一行里的多个column)

## flex来了

&emsp;&emsp;flex-即flexible,弹性的，灵活的，又叫弹性盒布局(flexible box layout)，这种布局方式主要有以下特点：

1. 块级布局侧重垂直方向，行内布局侧重水平方向，而flex是与方向无关的(来源于后续提到的一个重要属性flex-direction)
2. flex布局可以实现空间自动分配、自动对齐(弹性灵活的体现，与后续提到的flex-grow,flex-shrink，flex-basis属性有很大关系)
3. flex适用于简单的线性布局(左右或上下布局)，更复杂的布局需要用grid布局(如瀑布流布局)
## 基本概念
<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex0.png"></div>

&emsp;&emsp;关于flex布局中，有以下几个基本常识值得注意：

1. 如图所示flex布局中有两条轴线贯穿了外层容器，分别为主轴和侧轴(这里主轴不一定是水平方向的轴线，具体有flex-direction而定，即主轴和侧轴的方向是不一定的，也印证了flex布局是方向无关的)
2. 主轴的尺寸为main size,侧轴的尺寸为cross size
3. 主轴的起点和终点分别为main start和main end,对应的侧轴的起点和终点分别为cross start和cross end
4. 应用了`display:flex`属性的元素叫做 flex container，即flex容器，它里面的子元素叫做flex item
5. flex 容器的`宽度总是自动扩张到最大值`(注意是宽度，不管主轴是哪个方向，它的宽度总是会扩张到最大值,即使这个容器元素是行内元素如span,而子元素的宽度受多个属性影响，如flex-direction,align-items,flex-grow等，有时候看起来容器元素的宽度并不等于item元素宽度的和，如下图2所示)
6. flex 容器内的子元素即flex-item在不指定height,width的情况下总是自动收缩的(height本来就自动收缩，但当父元素display:flex后，这些子元素的宽度也都自动收缩，而没有了原本块级元素在宽度上自动扩张的特性)，同样的，这里`不区分主轴的方向，height,width总是自动收缩`
7. display:flex以后 float,clear,vertical-align等属性均失效


## flex container的六大属性

### flex-direction—方向

&emsp;&emsp;flex-direction 指定了子元素排列的方式，可以为row(按行展示)/column(按列展示)/inherit/row-reverse/column-reverse

例1. 当flex-direction为row时，显示效果如下：

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/image/css/2018/05/25flex2.png" /></div>
<div align="center">图2</div>

其中html和css代码如下：

```
<div class="container">
    <div class="item">item1</div>
    <div class="item">item2</div>
</div>

.container{
  display:flex;
  border:1px solid red;
  height:300px;
}
.item{
  border:1px solid black;
}
```

例2.当为.container添加flex-direction:cloumn属性后，排列效果如下：

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/image/css/2018/05/25flex1.png" /></div>
<div align="center">图3</div>

&emsp;&emsp;图2，图3中的flex-item都没有指定width和height值，但我们通过border可以看到看到，图2中的垂直方向的空间自动填满了container的宽度，即container此时在主轴方向的长度，图3中的水平方向的空间自动填满了container的高度，同样的，这也是container此时在侧轴方向的长度，这个现在和后续提到的`align-items`有关，不过我们暂时可以做出如下总结：
默认情况下，flex-item元素在其侧轴方向的长度总是自动扩张的(当然，后续会知道，只是默认情况，稍微改变align-items属性值，表现就会不一样~)




### flex-wrap

&emsp;&emsp;用于控制是否可以换行(按row排列时)/列(按column排列时)，当flex-wrap是nowrap时，所有items总是自动缩小而不会换行(列)

### flex-flow

&emsp;&emsp;flex-direction和flex-wrap的缩写，如flex:row wrap实际上同时设置了 flex-direction:row和flex-wrap:wrap;

### justify-content

&emsp;&emsp;主轴方向的对齐方式，可取:flex-start/flex-end/center/space-around/space-between,默认为flex-start

### align-items

&emsp;&emsp;侧轴方向的对齐方式，可取：flex-start/flex-end/center/stretch/baseline,默认为stretch(填满父空间,`前提是不指定height值,当指定了height值时，会以该值为主`),也就是之前提到的`默认情况下，flex-item元素在其侧轴方向的长度总是自动扩张的`

#### align-itmes:stretch和height同时存在时

&emsp;&emsp;为了测试指定height时，align-items：stretch还是否会对元素项产生效果，进行了以下测试：

```
 <div class="outer">
    <div>item1</div>
    <div>item2</div>
    <div>item3</div>
    <div>item4</div>
</div>

.outer{
    align-items:stretch;
}
.outer div:nth-child(1){
  height:200px;
}
```
<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex9.png" /></div>

&emsp;&emsp;如图所示，由于align-items是stretch(也是默认值),所以除item1元素外的所有元素在侧轴方向都自动拉伸到最大了，而item1之所以没有自动拉伸，是因为对其显示设置了高度，此时可以理解为height属性的优先级要更高。

### align-content

&emsp;&emsp;align-content属性和justify-content，align-items是类似的，都是控制item元素之间的摆放方式，只是该属性只有在主轴方向上有多个轴线时才有效，如flex-direction是row时，如果item有换行则align-cnotent属性值有效，当flex-direction是clolumn时，如果item有换列，则align-content有效，其中属性值可以取flex-start/flex-end/center/space-around/space-between/stretch

> 注意：justify-content是用于控制属于不同的主轴线(可以想象为平行与主轴线)的元素在侧轴方向的的排列方式，其属性值包含space-around/space-between，没有stretch,而align-items是用于控制单个元素在侧轴方向的摆放的，其属性值包含stretch,不含space-around/space-between，align-content则用于控制当同一个侧轴有多个元素时，这些元素之间的摆放方式，它们即可取space-around/space-between，又可取stretch(当每一个侧轴都只穿过一个元素时(即没有多个主轴线，没换行/列)，该属性失效)

#### align-content和align-items属性同时指定时
&emsp;&emsp;以flex-direction:row时为例，当item有多行时，align-content和align-items是同时有效的，align-content控制行于行之间的排列关系，align-items控制元素在一行中的位置，可以看下以下例子：

html和css部分如下：


```

  <div class="outer">
    <div>item1</div>
    <div>item2</div>
    <div>item3</div>
    <div>item4</div>
  </div>

.outer{
  display:flex;
  border:1px solid red;
  height:300px;
  align-items:center;
  flex-wrap:wrap;
  align-content:flex-end;
}
.outer div{
  width:200px;
  border:1px solid black;
}
.outer div:nth-child(1){
  height:200px;
}

```

效果如下所示：

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex4.jpg" /></div>
<div align="center">图4</div>

&emsp;&emsp;可以看到，现在item1和item2是处于一行的，item3和item4是处于一行的，由于align-content是flex-end,所以所有行依次排列在侧轴的终点处，同时由于align-items为center，所以item2处于它所在行的垂直方向的中点处(**原来并非相对于它的父元素垂直居中，而是相对于它所在的行垂直居中**)

&emsp;&emsp;接下来，再看一下把align-items:center去掉的效果

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex5.png" /></div>
<div align="center">图5</div>

&emsp;&emsp;如图5所示，和刚才不同的是，item2在没有指定高度的情况下自动拉伸为何item1的高度(指定了200px)一样了，其实还是因为align-items的效果，它虽然没有显示指定，但它的默认值是stretch，所以在它所处的行中在垂直方向自动拉伸了。

&emsp;&emsp;所以，当align-content有效时，不要以为align-items就无效了，只是它们的作用不同而已，一个控制不同行元素相对于父元素(flex-container)的排列方式，一个用于控制元素在所处行中的垂直方向的位置(这里指的flex-direction为row,flex-direction为column时请自行推算~)

## flex item的六大属性

### flex-grow:多余空间自动分配比例

&emsp;&emsp;用于控制当主轴方向空间过多时，元素在主轴方向的长度自动增加；如果多个元素同时指定了flex-grow，则`多余的空间`会按照各自flex-grow值的比例`自动分配`

> 默认值：0

### flex-shrink:超出空间自动收缩比例

&emsp;&emsp;当主轴方向空间不够时，使元素在主轴方向的长度自动收缩(即使为元素项设置了显示宽度width值，依然会根据需要自动收缩，不同于之前container中的align-items属性值stretch会低于height的优先级)，以使得总长度能够适应container的长度(当应用了flex-wrap:wrap时，一般是不会自动收缩的，因为这时候自动换行/列了，不会空间不够)

> 默认值：1

#### flex-shrink和flex-wrap配合使用

&emsp;&emsp;flex-shrink的概念已经明了，看上去功能比较单一，不过不同的属性值之间的搭配还是会带来很多样化得效果的，尤其是这里就来看一下flex-shrink和flex-wrap的配合使用。

##### flex-shrink:0和flex-wrap:nowrap

&emsp;&emsp;下面看一下flex-shrink为0并且container没有设置flex-wrap属性时的效果:

&emsp;&emsp;html和css如下：

```
 <div class="outer">
    <div>item1</div>
    <div>item2</div>
    <div>item3</div>
    <div>item4</div>
  </div>

.outer{
  display:flex;
  border:1px solid red;
  height:300px;
  align-content:flex-end;
}
.outer div{
  width:150px;
  
  border:1px solid black;
  flex-shrink:0;
}
.outer div:nth-child(1){
  height:200px;
}
.outer div:nth-child(2){
}

```

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex6.png" /></div>
<div align="center">图6</div>

&emsp;&emsp;如上图所示，当设置item的flex-shrink为0时，说明子元素项不会自动收缩，造成的结果就是所有元素项按自身原本的尺寸依次排列，可能会有主轴上总长度超出container在该放向的长度的可能。

##### flex-shrink:1和flex-wrap:nowrap

&emsp;&emsp;接下来，将css中设置的flex-shrink:0去掉(等同于设置了flex-shrink:1,因为这是默认值)，此时效果如下：

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex7.png" /></div>
<div align="center">图7</div>

&emsp;&emsp;如上图所示，此时，各子元素项的宽度(在主轴方向的长度)明显收缩了(小于为其设置的width:150px)，这就是因为默认的flex-shrink:1使得超出的部分均分在了所有子元素项的宽度上。

##### flex-shrink:1和flex-wrap:wrap

&emsp;&emsp;最后，再为container添加flex-wrap:wrap看下是否又会有什么不同呢？

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex8.png" /></div>
<div align="center">图8</div>

&emsp;&emsp;如图8所示，此时子元素项懂得在合适的位置自动换行了，因此也就不存在宽度超出父元素宽度范围的情况了，所以即使设置了flex-shrink也不会出现长度收缩了~

### flex-basis 原始大小

&emsp;&emsp;我们都知道为container设置了display:flex后，子元素项的宽度正是恰好包裹自身内容的宽度，相当于是自动的，设置flex-basis后则类似于设置了width值，可以显示指定宽度，取值可以为绝对单位或是百分比(可用于栅格效果)

> 默认值：auto

### flex 缩写

&emsp;&emsp;flex属性即为flex-grow,flex-shrink,flex-basis三个属性的缩写

### order 指定次序

&emsp;&emsp;order用于指定子元素项在兄弟元素中排列的顺序，应用该属性可以简单的实现双飞翼布局

### align-self 自身的对齐方式(特殊化自身元素)

&emsp;&emsp;align-self指定元素自身在侧轴上的对齐方式，可以用来`覆盖align-items属性的值对自身设置的效果`。

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex10.png" /></div>
<div align="center">图9</div>

&emsp;&emsp;如图9所示，当align-items为stretch，item1的align-slef为center时，除item1外的所有元素在侧轴方向的对齐方式都是自动延伸的，而item1元素因为应用了align-slef:center,明显是垂直居中的。

&emsp;&emsp;想了解不同布局的flex实现方式的话，请戳[进一步了解flex布局—来实现这些常见布局吧](https://juejin.im/post/5b0d6ca76fb9a009fd0e9329)~
