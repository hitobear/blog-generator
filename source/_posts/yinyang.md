---
title: copy一个精致的单个div实现的阴阳图
---
### css画阴阳图
学着[CSS-tricks](https://css-tricks.com/examples/ShapesOfCSS/)的阴阳效果，自己也画了一个作为练习，虽然实现方式是copy的原效果的，哈哈，最起码能理解整个效果了，而且能自己背着copy下来了(( ╯□╰ )),还是很开心的，这里对整个实现的过程做一个小总结
### 目标效果
最终呈现效果是黑白阴阳图，最外层大圆所占空间宽度是100px,第二层的两个凸起圆所占宽度为48px,最内层的小圆直径是12px.
### 实现阴阳图
> 画一个下黑上白的div框  

从最终效果里可以看到，这应该是由一个正方形的div框演变而来的，这个div框的下边一半是黑色的，上边一般是白色的,那么如何实现呢？脑海里可能会闪过几种不同的实现方式，比如通过两个div合成，上div背景白色，下div北京黑色，再比如只用一个div的话，考虑填充色中上一半白色，下一半黑色，用颜色渐变来实现就好了，其实好像还有更简单的办法，考虑到最终效果知识下边一半是黑色而已，并没有说下边的黑色一定是内容区啊，所以这里完全可以用更加通俗易懂的边框来实现，把整个下半部分都当做边框就可以了，所以这块儿就通过**纯border的设置来实现这个效果**
```
<style>
#yinyang{
      border-color:black;
      border-width:2px 2px 50px 2px;
      height:48px;
      width:96px;
      border-style:solid;
      position:relative;
      background:white;
    }
</style>
<div id="yinyang"></div>
```
> 完成最外层大圆效果

由正方形变成圆形，毫不犹豫，只要设置`border-radius`属性就可以了，这里我们为`div#yinyang`添加属性设置`border-radius:50%`即可(**这里设置为大于50%也可以,浏览器会自动显示为各个border的圆角交接为止,不会互相超出**)

> 添加中层圆效果

第二层分别左右两侧各有一个中圆，设置它们的宽度为48px(去除了外层圆的2px的边框),同样是圆形，这里当然可以考虑左右各添加一个绝对定位的div来实现，但想到`before,after`这两个伪元素(****)，他们不正可以用来为div添加内容吗？所以这里完全可以利用他们，节省两个div~
```
 #yinyang::before{
      content:'';
      width:48px;
      height:48px;
      background:black;
      position:absolute;
      border-radius:100%;
      top:50%;
      left:0;
    }
     #yinyang::after{
      content:'';
      width:48px;
      height:48px;
      background:white;
      position:absolute;
      border-radius:100%;
      top:50%;
      right:0;
    }
```
这里有一点需要注意下，刚添加玩before,after伪元素后，我没有看到任何效果，尝试了好几次才发现原来少了一个`content`属性，这里就是常常被我忽略的：**before after伪元素一定要加上content属性，它的一系列属性效果才会生效，即使设置了边框，哪怕这个content的内容是空字符串也可以**
> 实现小圆效果

最后还差最内层的小圆了，最开始想到的是像上一层一样，再添加个before，after伪元素来实现小圆，但由于上一层已经用了这两个伪元素了，而before里再嵌套before是无效的，所以这种方式行不通了。。那么再换一种方式思考，不就是中圆里嵌套一个小圆吗，为什么要分别画两个圆呢？完全可以把内层的圆和外层的圆环当作同一个圆的两部分啊，内层的圆作为圆的内容，外层的圆环是圆的边框就可以了，通过设置一个圆的背景色和边框色就可以实现了~所以现在来改一下上一步的对伪元素的属性设置，为其添加边框，设置边框和宽度的比例等等
```
#yinyang::before{
      content:'';
      width:12px;
      height:12px;
      border:18px solid black;
      background:white;
      position:absolute;
      border-radius:100%;
      top:50%;
      left:0;
    }
     #yinyang::after{
      content:'';
      width:12px;
      height:12px;
      border:18px solid white;
      background:black;
      position:absolute;
      border-radius:100%;
      top:50%;
      right:0;
    }
```
### 自动旋转的阴阳图
目前为止，一个完整的阴阳图已经可以显示了~那现在可以考虑为它添加一点简单的动画，比如让它自己不停的匀速旋转，这里利用了`css3`的`animation`和`transform`属性，相关代码如下
```
 @keyframes spin {
       0% {
        transform: rotate(0deg);
        }
      100% {
        transform: rotate(360deg);
      }
}
 #yinyang{
      ...
      animation: spin 1.4s infinite linear; 
    }
```
到这里效果就完成了，[这里](http://jsbin.com/jaliwex/edit?html,output)可以预览完整效果和查看代码,最后再总结一下阴阳图效果实现中的tips

> Tips
1. 可以考虑设置不同的`border`宽度来取代某些简单情况的渐变实现
2. 考虑用`before` `after`伪元素代理空的div子元素设置效果(注意设置'content')
3. 考虑用内容和边框分离表现来代替div的嵌套