---
title: 从一次利用块级元素绝对定位引发的bug感悟出元素的包裹性宽度
---
这篇总结的起因为我在写导航栏时遇到的一个bug,开始时，导航栏的相关代码如下：
```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>JS Bin</title>
  <style>
    a{
        text-decoration:none;
        color:white;
    }
    li{
      list-style: none;
    }
   .header nav{
        float:right;
        padding-top:7px;
    }
    nav>ul>li {
        float:left;
        margin:0 17px;
    }
    nav>ul>li>a {
        
        border-top:   3px solid transparent; 
        border-bottom: 3px solid transparent;
        padding-top:5px;
        padding-bottom:5px;
        display:block;
        font-weight:bold;
        font-size:12px;
        color:inherit;
    }
  </style>
</head>
<body>
 <div class="header clearfix" id="header">
        <div class="header-inner">
            <nav>
                <ul class="clearfix">
                <li><a href="#">关于</a></li>
                <li><a href="#">技能</a></li>
                <li><a href="#">作品</a></li>
                <li><a href="#">博客</a></li>
                <li><a href="#">日历</a></li>
                <li><a href="#">联系方式</a></li>
                <li><a href="#">其他</a></li>
                </ul>
            </nav>
        </div>
    </div>
</body>
</html>
```
效果如下图一：
<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/image/css/2018/03/13nav0_20180313225537.png"></div>

<div align="center">图1</div>
接下来我要实现的效果是在博客这一列表项下添加3个子列表，所以我对html简单了添加了以下内容：

```
<li>
       <a href="#">博客</a>
      <ul>//新增ul元素
             <li>博客1</li>
             <li>博客2</li>
              <li>博客2</li>
       </ul>
 </li>
```

现在，导航栏效果如下图2：

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/image/css/2018/03/13nav2_20180313230704.png"></div>

<div align="center">图2</div>
可以看到，添加了ul列表后，基本实现了子级列表项，但可以明显的看到，此时原列表项博客，收到了子列表项宽度的影响，被明显的拉宽了，这样显然不优雅，那么有没有什么办法，使其不受子列表项的影响，自身宽度保持不变呢？这时候想到了，将子列表项绝对定位，使其脱离文档流， 这样它的宽度肯定不会影响到父元素的宽度了，于是为字列表项ul元素添加了相对于父元素li的绝对定位，相关CSS代码如下：

```
nav>ul>li {
        position:relative;
    }
    nav>ul>li>ul{
      position:absolute;
    }

```

本以为这下可以完成任务了，结果刷新后，效果却如下图3所示
<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/image/css/2018/03/13nav3_20180313231556.png"></div>
<div align="center">图2</div>
是的，正如图中所示，绝对定位解决了列表项宽度受影响的问题，但却引入了新的问题，子列表项的宽度太窄了！！那么这是什么原因呢？实验后发现这个表现有两方面的综合原因。

## 块级元素绝对定位后，它本身及其所有后代块级元素会有包裹性
最开始的块级元素及其子元素的代码如下：
```
 .pos{
      
      border:1px solid red;
    }
    .first{
      border: 1px solid green;
      margin-bottom:10px;
    }
    .seconed{
      border: 1px solid black;
    }
    .seconed div{
      border: 1px solid #888;
      margin-bottom:5px;
    }

<div class="pos"><div class="first">第一个子元素div</div>
    <div class="seconed"><div>第二个子元素的子元素1 </div>
    <div>第二个子元素的子元素2 </div</div>
  </div>
```
效果如下图4所示：
<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/image/css/2018/03/13nav4_20180313234058.png"></div>

<div align="center">图4</div>

然后为了观察绝对定位的影响，为最外层的div元素设置为绝对定位，如下代码：

```
.pos{
      position:absolute;
     ......
    }
```
之后，效果如图5：
<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/image/css/2018/03/13nav5_20180313234109.png"></div>

<div align="center">图5</div>

对比图5发现，添加绝对定位以后，不管是其本身还是直接子元素`first`类的`div`,或是其孙元素`seconed`类的`div`的子元素，其宽度均收缩为所包含文字需要的宽度，不再主动填充外层容器，展现了其宽度的包裹性。
## 块级元素绝对定位后，它本身的宽度依然不能超过父元素的宽度

这个特性其实是通俗特性的扩展，及无论是否脱离文档流，div元素本身的宽度都不能自动超出父元素的宽度，除非显示指定宽度值。
在最开始的导航栏一例中，设置了绝对定位后，因为脱了了文档流，而恰好其父元素`li`又设置过`float:left`属性，因此`li`的宽度本身就又包裹性，**(浮动元素的宽度包裹性和绝对定位元素的宽度包裹性异曲同工)**,只会包裹住其子元素a标签的内容，(因为ul绝对定位后脱离了文档流，没有绝对定位时还在文档流中，是可以包裹住的，所以绝对定位前ul列表的宽度是正常的)，所以ul标签的宽度受限于父元素li的宽度，当然很窄了！！

## 如何解决层层包裹性带来的文字自动换行的bug?
经过上面的分析，这里bug的主要原因还是 `ul`绝对定位以后元素自身脱离文档流，导致浮动的父元素`li`(本身也有包裹性)未将子元素`ul`宽度包裹住，只包裹了`a`标签的宽度，所以，虽然此时ul按理说应该能包裹住子元素的宽度的，但是因为其父元素li宽度过窄，导致ul及其子元素的宽度也受限，其文字内容不得不自动换行了。
这里为了解决这个bug，可以换一种思路，只要文字不自动换行是不是就可以了？添加如下样式，即增加
`white-space:nowrap;`属性设置
```
    nav>ul>li>ul{
     .....
      white-space:nowrap;
    }

```
现在效果如图6所示：

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/image/css/2018/03/13nav6_20180314000504.png"></div>

<div align="center">图6</div>

bug完美解决了~