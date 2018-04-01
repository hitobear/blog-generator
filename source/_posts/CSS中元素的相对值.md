---
title: CSS中元素的相对值
---
css中的单位有相对和绝对之分，如`em` `rem` 是相对单位,`px`是绝对单位,而相对又有不同的基准值之分，如`font-size`为百分比时，是相对于其父元素的`font-size`计算值，而`line-height`为小数时，是相对于本元素的`font-size`计算值的，一般说来，相比于绝对单位,使用相对单位能够更加灵活的控制元素的大小,能够以更少的代码达到自适应效果,同时使各元素或各属性之间的大小关系更加清晰,直观。
## 使用绝对单位`px`来设置属性值
先来看一个简单的只包含基本问候语的页面，页面效果如下:
<div align="center"><img src="http://p4a8bakov.bkt.clouddn.com/px.png" width="300" height="300"></div>
实际的代码如下:
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
    *{
        margin:0;
    }
    #wrapper{
        text-align:center;
        font-size:16px;
        line-height:24px;
    }
    body{
        display:flex;
        justify-content:center;
        align-items:center;
        height:100vh;
        background:linear-gradient(rgba(177,206,90,0.5),rgba(177,206,90,1));
    }
    h2{
        text-align: center;
        font-size:24px;
        line-height:36px;
        margin-bottom:40px;
    }
    p.content{
        text-align:center;
        width:200px;
        margin-bottom:20px;
        font-size:18px;
        line-height:25px;
    }
    button{
        padding: 6px 16px;
        border: 1px solid #446d88;
        background: #58a linear-gradient(#77a0bb, #58a);
        border-radius: 4px;
        box-shadow: 0 1px 5px gray;
        color: white;
        text-shadow: 0 -1px 1px #335166;
        font-size: 20px;
        line-height: 30px;
        display:inline-block;
    }
    </style>
</head>
<body>
    <div id="wrapper"><h2>CSS之旅</h2>
    <p class="content">欢迎来到CSS世界,我们一起成长吧~</p>
    <button>Welcome</button></div>
</body>
</html>
```
可以看到，在这里的代码中，包括行高,字体大小，宽度，高度，内边距等等所有的长度单位都用的`px`,虽然看上去大小很直观，像素值一目了然，但是与此同时后期的维护体验是相当的差。。
## line-height 的相对值
可以看到，上述的代码中，共有三个地方都出现了`line-height`属性的设置，包括外层的`div`,内层的`button`和`p`，而且经过稍微的运算可以得出`lineheight`的值是`font-size`值的`1.5`倍,那么如果因为某种原因，我需要将dev的`font-size`值由`16px`改为`12px`,为了确保行高相对于字体的大小和原来不变，需要重新计算出`12px`的`1.5`倍的值，其他的字体大小改变同样要经过这个计算，那么既然我们已经直到行高相对于字体大小的比例是固定的，有没有什么办法不用每次`font-size`值改变时，行高都需要手动重写计算呢？幸好，是有的~~
### line-height设为百分比
`line-height`值可以设置为百分比，此时它的计算值为**元素本身**的`font-size`值的百分比大小。
上一节的情况，由于想要让行高是字体大小的`1.5倍`，只要将行高属性设置为`line-height:150%`即可,这样，当元素的`font-size`值无论如何变化，`line-height`的值都无需更改，也能保持行高为元素字体的`1.5倍`(浏览器会自动计算)

### line-height设为小数
经过了上述改进，已经可以做到`font-size`改变时，无需改变`line-height`了，但现在还有一个问题，可以看到`p`元素和`buttong`元素的`line-height`值和`div`元素的`line-height`值是一样的,都是相对于各自元素的字体大小的`150%`即`1.5`倍，如果我们确定这几个元素中每个元素的行高和字体比是一样的，那么当这个比例由`1.5`改为`1.6`时，需要同时更改这三个元素的`line-height`属性从`150%`到`160%`,这个过程看上去也有些冗余，有没有其它办法呢？当然也是有的~ 
这三个元素有个特点，即`p`和`button`元素是`div`元素的子元素，这时候为了避免上述情况的冗余变更，可以将`line-height`属性值由`百分比形式变为小数形式` 

**`line-height`设为小数时，可以让继承其属性的子元素直接继承该小数点，而line-height设为百分比时，继承该属性的子元素继承的则是父元素计算好的实际像素值**，这是`line-height`设置为小数点和百分比的本质区别。

举例来说，在本例中`p`时`div`元素的子元素，如果父元素`div`的行高设置为`150%`,不为`p`元素设置`line-height`属性的话，则`p`元素默认继承父元素的`line-height`计算值(即16px*150%=24px)，则无论`p`元素的`font-size`值为多大，行高都保持`24px`，显然时不合理的。这时，为了既能继承父元素的行高和元素字体大小比，又能与元素本身的字体大小相关，只需**将父元素的行高设置为小数形式**即可,给`div`设置`line-height:1.5`,这样`p`元素默认继承的是`line-height:1.5`,**它的计算值是自身的font-size值*1.5**

采取为父元素设置行高为小数点的形式，这样无需为子元素设置`line-height`,也会默认继承父元素的行高字体比，(因为**`line-height`属性默认就是继承的**，`button`元素需要设置`line-height:inherit`，显示指定继承，否则会被浏览器添加的用户样式覆盖),这中方式既省去了设置多个元素的`line-height`属性值，又避免了行高字体比变化时的代码冗余变更问题，当比例变化为1.6时，只需显示改变父元素`div`的行高为`line-height：1.6`即可。

## font-size的相对值
示例代码中，父元素`div`的`font-size`值是`16px`,`button`元素的`font-size`值是`20px`,是其父元素的`125%`,同样地，如果想让buttong元素的字体大小固定为父元素字体大小的`1.25`倍的话，当父元素的字体值改变时，这时不得不重新计算`button`元素的大小并重新设置该属性值，为了避免这个问题，font-size也支持相对值，即`百分比格式的数值`,**当`font-size`设置为百分比时,是相当于其父元素字体的百分比**


## em和rem
除了某些元素可以设置为百分比外，还有两个相对长度单位，`em`和`rem`,`em`和`rem`都是灵活，可扩展的长度单位，直接相关于字体的默认大小或指定的大小值，可应用于元素的`padding`,`marign`,`border-width`,`line-height`等属性。
### em
当使用em单位时，实际像素值是em值乘以使用em单位的元素本身的字体大小，如一个元素的`font-size`值是`12px`,则当padding值设置为`0.2em`时，实际内边距的像素值是`12*0.2`即`2.4px`。
应用em，可以将该例中button相关的`padding`,`margin`值设置为相对于字体`font-size`的像素值的特定比例大小(边框的宽度无需用`em`表示，固定为`px`即可)

将本例中的`button`元素改良为`em`值后，相关属性设置如下:
```
button{
        padding: .3em .8em;
        border: 1px solid #446d88;
        background: #58a linear-gradient(#77a0bb, #58a);
        border-radius: .2em;
        box-shadow: 0 .05em .25em gray;
        color: white;
        text-shadow: 0 -0.05em 0.05em #335166;
        font-size: 125%;
        line-height: inherit;
        display:inline-block;
}
```

### rem
`rem`和`em`极其类似，当使用 `rem`时，实际的像素大小取决于页面根元素的字体大小，即 html 元素的字体大小，像素值等于根元素字体大小乘以 rem 值

综上，`rem`和`em`使得内外边距，边框等属性都能够相对于字体大小进行设置(等同于可以设置相对字体大小的百分比，用小数点+em单位来表示)，不同之处在于，`em`是相对于元素本身的字体大小进行设置，而`rem`是相对于页面根元素(`html`)的字体大小进行设置的，要想区分要用`em`还是`rem`单位，只需确定该属性是和元素本身的字体大小相关联还是和`html`元素的字体大小相关联

综上，CSS中应用相对值时，最重要的还是分析清楚属性之间的相关性。
[代码请戳](https://github.com/hitobear/cssgarden/blob/master/realative/index.html)

