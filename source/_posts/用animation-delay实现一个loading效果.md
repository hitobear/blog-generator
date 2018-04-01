
---
title: 用animation-delay实现一个loading效果
---
要实现的loading效果是这样的[点击这里查看](https://jsbin.com/nulasok/edit?html,output),这里对如何制作这样一个loading效果做一下总结
## 效果分析
1. 打开Console的Animations选项卡，以10%的速率放慢动画观察效果
2. 发现动画是由内外两个圆圈组成，而且内外圆圈效果几乎一样，只是有不同的动画延迟
3.圆圈由小到大，逐渐淡出
## 效果实现

### 外层wrapper
首先画一个wrapper,让动画的两个圆圈在这个wrapper中绝对居中
```
 #wrapper{
      width:100px;
      height:100px;
      border:1px solid red;
      position:relative;
}
<div id="wrapper">
</div>
```
### 第一个圆圈
画出第一个圆圈，让其居中于外层wrapper,且自带动画效果，由小到大，逐渐淡出，动画效果呈线性，且无限循环
```
.circle{
      width:10px;
      height:10px;
      background:black;
      border-radius:50%;

     //绝对定位居中于父元素
      position:absolute;
      left:0;
      right:0;
      top:0;
      bottom:0;
      margin:auto;

      //圆圈的动画效果
      animation: s 1.5s linear infinite;
    }

//动画定义
@keyframes s{
      0%{width:0px; height:0px; opacity:1;}
      100%{width:100px; height:100px; opacity:0;}
    }
<div id="wrapper">
  <div class="cirle"><circle>
</div>
```
### 第二个圆圈
将第二个圆圈设为和第一个一样的效果，只是加上合适时间的动画延迟
```
#wrapper:nth-child(2){
      animation-delay:0.75s;
    }
<div id="wrapper">
  <div class="cirle"><circle>
  <div class="cirle"><circle>
</div>

```
目前为止，loading动画已经基本实现，接下来，做一些些优化

### 代码优化

#### 用伪元素代替div
效果中，wrapper元素中的两个圆圈只是用来设置了元素样式，没有充当其他元素的容器，所以这里我们用before,after伪元素来替代两个circle类元素，注意：**伪类元素要生效必须设置`content`属性**
#### 用loading类代替wrapper
用loading代替class,使元素更具有语义化

最终代码如下：
```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>loading animation</title>
  <style>
    .loading{
      width:100px;
      height:100px;
      position:relative;
    }
    .loading::before,.loading::after{
      content:'';
      width:10px;
      height:10px;
      position:absolute;
      background:black;
      border-radius:50%;
      left:0;
      right:0;
      top:0;
      bottom:0;
      margin:auto;
      animation: s 1.5s linear infinite;
    }
    .loading::after{
      animation-delay:0.75s;
    }
    @keyframes s{
      0%{width:0px; height:0px; opacity:1;}
      100%{width:100px; height:100px; opacity:0;}
    }
  </style>
</head>
<body>
<div class="loading">
  </div>
</body>
</html>
```
现在只要引入如下代码，就能引入loading元素了,具体效果可以看这里- [用JSbin打开]loading animation](https://jsbin.com/nulasok/edit?html,output)
```
<div class="loading"></div>
```
