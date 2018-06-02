---
title: 进一步了解flex布局—来实现这些常见布局吧
---

&emsp;&emsp;flex布局具有便捷、灵活的特点，熟练的运用flex布局能解决大部分布局问题，这里对一些常用布局场景做一些总结。

## web页面布局(topbar + main + footbar)

### 示例代码

&emsp;&emsp;要实现的效果如下：

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex11.png" /></div>
<div align="center">图1</div>

&emsp;&emsp;html代码：

```
   <div class="container">
     <header>header...</header>
     <main>内容</main>
     <footer>footer...</footer>
   </div>

```

&emsp;&emsp;css代码：

```
header{
  height:100px;
  background:#ccc;
}
footer{
  height:100px;
  background:#ccc;
}
.container{
  display:flex;
  flex-direction:column;
  height:100vh;
}
main{
  flex-grow:1;
}
```

### 应用的flex属性

&emsp;&emsp;本例中主要应用了以下flex属性：

1. flex-direction:column 使整体布局从上到下排列
2. flex-grow:1, 应用于main，使得main自动填充剩余空间

本例中应用以较少的css代码实现了传统的上中下页面布局，其中的关键通过flex-grow的使用避免了当main内容过少时footer部分会被提升到页面上方的问题(传统方式上可能需要靠绝对定位来解决了~)

## 每行的项目数固定并自动换行的列表项

&emsp;&emsp;要实现的效果如下：

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex12.png" /></div>
<div align="center">图2</div>

&emsp;&emsp;html代码：

### 示例代码

&emsp;&emsp;css代码：

```
ul{
  display:flex;
  flex-wrap:wrap;
}
li{
  list-style:none;
  flex:0 0 25%;
  background:#ddd;
  height:100px;
  border:1px solid red;
}

```

### 应用的flex属性

&emsp;&emsp;本例中主要应用了以下flex属性：

1. flex:0 0 25%,相当于flex-basis:25%，使得每一个列表项的宽度占外层容器(本例中的ul元素)的25%，因此每行最多能够排开四个列表项。

2. flex-wrap:wrap,使得每行填满时会自动换行

## 实现自动划分多余空间的列表项

&emsp;&emsp;本例的效果和上例中的图2很相似，只是每行为3个固定宽度的列表项，并且各列表项之间留有相同宽度的空隙

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex13.png" /></div>
<div align="center">图3</div>

&emsp;&emsp;传统方式上实现这种效果，不可避免的要用到负margin，那么现在来看了用flex实现的方式吧

### 示例代码

&emsp;&emsp;css代码：

```
ul{
  display:flex;
  flex-wrap:wrap;
  justify-content:space-between;
  border:1px solid black;
}
li{
  list-style:none;
  width:120px;
  background:#ddd;
  height:100px;
  border:1px solid red;
}

```

### 应用的flex属性

&emsp;&emsp;本例中主要应用了以下flex属性：

1. justify-content:space-between;使主轴方向的多余空间平均分配在两两item之间

## 平均分配空间的栅格布局

&emsp;&emsp;各大UI里栅格布局基本是必备的布局之一，平均分配布局又是栅格布局里最常用的布局，利用flex实现平均分配的栅格布局，关键之处就是利用它的自动收缩空间。

<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex14.png" /></div>
<div align="center">图4</div>

### 示例

&emsp;&emsp;html如下：

```
<div class="row">
         <div class="column">column1</div>
         <div  class="column">colum22</div>
         <div  class="column">colum322</div>
</div>

```

&emsp;&emsp;css如下：

```
.row{
  display:flex;
  flex-wrap:wrap;
  border:1px solid black;
}
.column{
  list-style:none;
  background:#ddd;
  flex:1;
  height:100px;
  border:1px solid red;
}
```

### 应用的flex属性

&emsp;&emsp;本例中主要应用了以下flex属性：

1. flex:1 这里针对item应用了flex:1,相当于flex:1 1 0%,而之所以不管各个column元素内容的宽度为多大，都能均分到相等的空间，正式因为相当于在设置了flex-grow:1使得剩余空间按相等比例自动分配的同时又设置了flex-basis:0%,才使得整个空间都平均分配了。

## 圣杯布局

&emsp;&emsp;传统的圣杯布局需要涉及绝对定位、浮动、负margin等几大最头疼属性，有了flex布局以后发现，原来这么简单的配方，也能实现这么复杂的效果哈~


<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex15.png" /></div>
<div align="center">图5 圣杯布局</div>

### 示例代码

&emsp;&emsp;html代码：

```
<div class="container">
       <main>main</main>
       <aside>aside</aside>
       
       <nav>nav</nav>
</div>

```

&emsp;&emsp;css代码：

```
.container{
  display:flex;
  height:100vh;
}
aside{
  width:50px;
  background:#ccc;
}
main{
  flex-grow:1;
  background:#def;
}
nav{
  width:80px;
  background:#ccc;
  order:-1;
}

```

### 应用的flex属性

1. 对main用flex-grow:1,使得中间主元素空间自动扩充
2. 对nav应用order:-1,使得order处于最左侧(html中main写在了最前，以利于优先加载主内容区)

## 元素水平垂直居中

&emsp;&emsp;如何让一个元素同时水平垂直居中？答案很多样也很复杂，但是在这么多样化得答案中flex绝对是最简单的一种了~



<div align="center"><image src="http://p4a8bakov.bkt.clouddn.com/flex16.png" /></div>
<div align="center">图6 水平垂直居中</div>

### 示例代码

&emsp;&emsp;html代码：

```
 <div class="container">
       <div class="inner">我是中间的内容</div>
 </div>

```

&emsp;&emsp;css代码：

```
.container{
  height:300px;
  width:300px;
  border:1px solid red;
  display:flex;
  justify-content:center;
  align-items:center;
}

.inner{
  border:1px solid black;
}

```

### 应用的flex属性

1. justify-content:center;使item元素水平居中
2. align-items:center;使item元素垂直居中

&emsp;&emsp;这么多场景都难不倒flex有木有，果然名不虚传~(&emsp;&emsp;想更详细的了解flex相关属性的话，请戳[flex入门—了解这些flex属性](https://juejin.im/post/5b0d6eab6fb9a009ea104bdd)~)