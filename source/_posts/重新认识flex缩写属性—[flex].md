---
title: 重新认识flex缩写属性—[flex]
---

## flex:1引发的疑惑


&emsp;&emsp;flex是flex-grow,flex-shrink,flex-basis三个属性值的缩写，所以我也曾以为只是单纯缩写而已，没什么复杂的，直到看了flex:1能实现各子元素均分外层容器空间的效果：

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex17.png" /></div>
<div align="center">图1</div>

&emsp;&emsp;html和css代码如下：

```
   <div class="container">
       <div class="inner">我是左侧的item</div>
       <div class="inner">我是右侧的item,我的内容比较多</div>
   </div>

   .container{
        height:300px;
        width:400px;
        border:1px solid red;
        display:flex;
    }

    .inner{
        border:1px solid black;
        flex:1;
    }
```

&emsp;&emsp;按照我最初的理解，flex:1相当于设置了flex-grow:1,flex-shrink:1(默认值),flex-basis:auto(默认值)，而如果对元素这样设置三个属性值的话，效果如下图2所示，明显是水平均分的，因为flex-grow为1只是表示均分多余的空间

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex18.png" /></div>
<div align="center">图2</div>

&emsp;&emsp;那么问题来了，flex:1并不等价于flex-grow:1,flex-shrink:1(默认值),flex-basis:auto啊，说好的缩写呢？

## flex是缩写

&emsp;&emsp;flex是flex-grow,flex-shrink和flex-basis的缩写，flex属性值可以只指定一个属性的值，而另外的属性值采用各自在flex属性中的的初始值，但是有一点要注意的是：flex属性中flex-grow和flex-basis的初始值和它们原始的默认值不同，至于为什么不同，mdn中有明确的说过`这样的设计是为了让「flex」缩写在最常见的情景下比较好用。`

## flex中对应各属性的初始值

### flex-grow

&emsp;&emsp;flex-grow用于设置各item项按对应比例划分剩余空间，若flex中没有指定flex-grow,则相当于设置了flex-grow:1

### flex-shrink

&emsp;&emsp;flex-shrink用于设置item的总和超出容器空间时，各item的收缩比例，若flex中没有指定flex-shrink,则等同于设置了flex-shrink:1

### flex-basis

&emsp;&emsp;flex-basis用于设置各item项的伸缩基准值，可以取值为长度或百分比，如果flex中省略了该属性，则相当于设置了flex-basis:0.

## flex的不同取值

&emsp;&emsp;flex的值的完整写法是[\<flex-grow> \<flex-shrink> \<flex-basis>],不过它的取值还有可能是单个数字或者单个百分比等，不同种类的取值所表示的意思是大有不同的。

### flex值为none

&emsp;&emsp;当flex为none时,等同于flex: 0 0 auto;

### flex值为auto

&emsp;&emsp;当flex为auto时，等同于flex: 1 1 auto;

### flex值为一个非负数字

&emsp;&emsp;当flex取值为一个数字，则该数字是设置的flex-grow值，其它两个属性用初始值，如flex:1时，等同于flex: 1 1 0%

### flex值为两个非负数字

&emsp;&emsp;当flex取值为2个数字时，相当于设置的flex-grow和flex-shrink值，flex-basis取值为初始值，如flex:1 0时，等同于flex: 1 0 0%

### flex值为一个数字和一个长度或百分比时

&emsp;&emsp;当flex取值为1个数字和1个长度或百分比时，设置的是flex-grow和flex-basis的值，flex-shrink值时初始值，如flex:1 20%,等同于flex: 1 1 20%


&emsp;&emsp;原来一个简单的缩写属性flex是有这么多花样的~那么我不想背它的初始值，不同值对应的缩放属性等等，有这么多原因，以后是不是不用flex就可以了，光明正大用flex-grow,flex-shrink,flex-basis就好了？当然是可以了~不过用flex至少还是有它的好处的，比如说看上去简洁(一个flex属性能有这么多不同效果进而显得高大上),还有重要的一点，同样的一个单词能完成三个单词的工作，能使代码少啊:）
