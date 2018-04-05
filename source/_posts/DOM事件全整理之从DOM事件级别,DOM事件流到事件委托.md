
---
title: DOM事件全整理之从DOM事件级别,DOM事件流到事件委托
---
&emsp;&emsp;JS编程中常常接触到DOM事件，虽然只是事件而已，但是处于历史原因，它是足够复杂的，也足够强大的，由于自己之前总是对DOM事件的一些概念模糊不清，所以这里来做一下总结啦

## DOM级别与DOM事件

&emsp;&emsp;我们常听到DOM0级事件，DOM1级事件，DOM2级事件处理，其实这里的DOM0，DOM1，DOM2和DOM级别是密切相关的。

&emsp;&emsp;最开始的时候，IE4和Netscape等浏览器各自实现自己的一套DOM api，没有一个统一的标准，对于开发者和用户来说是一件头疼的事，于是，1998年，W3C综合了各家的现有API,制定了DOM1级(DOM Level 1)标准。DOM1级比较简洁，由两个模块组成：DOM核心（DOM Core）和DOM HTML，其中各个事件相关的信息作为方法包含在在DOM元素中进行说明的，详细可以了解W3C的相关[章节](https://www.w3.org/TR/2000/WD-DOM-Level-1-20000929/level-one-html.html).

&emsp;&emsp;DOM2级的目标要宽泛很多，引入了多个新模块，其中包括DOM Events，即在DOM2的时候，单独把事件作为一个模块，并针对DOM1级事件部分作了很大的升级，不再只是简单的告诉我们有哪些事件可以用，而是使事件的标准更加详细，如新增了事件流，事件捕获，事件冒泡，事件取消等机制和规范。

&emsp;&emsp;DOM Level 3并没有对事件做任何修订(可能是因为认为DOM Events已经足够成熟了吧),所以我们知道现在用的还是DOM Level 2的事件标准。

&emsp;&emsp;到这里可以做一下总结，DOM API 是有DOM1级，DOM2级，DOM3级三个标准的，与之对应的每一个标准中DOM事件相关的部分我们叫做DOM1级事件处理，DOM2级事件处理(刚才也说了，DOM3级事件处理不存在)，那么标准是从DOM Level 1开始的，我们说的DOM0又是什么呢？可以说这只是公认的一种说法，即在又事实标准之前的事件处理，我们叫做DOM0级事件处理。

### Tips

&emsp;&emsp;因为DOM1只是对之前各大厂商的DOM api做了以下整理而实施的标准，所以通常我们认为DOM1的事件处理和DOM0的事件处理是一样的。

## 事件处理程序

&emsp;&emsp;事件处理程序就是响应某个事件的函数，DOM中的事件处理程序有多种方式，大概可以分为以下五种类型。

1. HTML事件处理程序 
2. DOM0级事件处理程序
3. DOM2级事件处理程序


### HTML事件处理程序

```
<button onclick="alert(hello world!)"></hello>
```

&emsp;&emsp;像上述代码这样，直接将事件函数写到HTML中元素的属性上，就是HTML事件处理程序，这里双引号中的部分是事件触发后**要执行的代码，它实际上是由JS引擎由`eval()`调用的**，所以它是全局作用域。

&emsp;&emsp;这样的事件处理有一个明显的缺点，即当JS代码太复杂时，将大段JS代码卸载HTML中显然不合适，于是有了下面这种写法：

```
<button onclick="doSomething()"></hello>
```

&emsp;&emsp;这样虽然解决了嵌套代码过长的问题，但又引来了另一个问题，即时差问题—,如果用户在界面刚出现就进行点击，而JS还没有加载好的话，就会报错。

&emsp;&emsp;此外，很重要的一点是，这种写法，一个函数的改变，可能同时需要js和html的改变，严重违背了轻耦合的原则，综上，我们有了DOM0级事件处理。

### DOM0级事件处理


```
<script>
var btn=document.getElementById("#btn");
btn.onclick=function(){
    alert(hello world!)
}
</script>
```
&emsp;&emsp;可以看到，这种方式中可以把事件处理相关部门都放到js中，并且这里的事件处理程序是作为btn对象的方法的，是局部作用域。

&emsp;&emsp;但是现在，我依然面临着问题，如果有对这个元素的单击事件添加两个处理函数，这个就无法帮我实现了，而且即使不需要添加多个处理函数，我也不太敢轻易的添加事件，除非我非常确定，别人写代码时不会涉及到这部分(因为一不小心可能会覆盖他人之前对这个元素的该事件添加的处理函数)。

### DOM2级事件处理程序(不支持IE)

&emsp;&emsp;进一步规范后，有了DOM2级事件处理程序，我们可以通过类似如下代码，对一个元素的同一个事件添加多个处理程序

```
var btn=document.getElementById("#btn");
btn.addEventListener("click",function(){
    alert(hello world!)
})

btn.addEventListener("click",function(){
    alert(hello world2!)
})
</script>

```

&emsp;&emsp;通过DOM2级的`addEventListener`方法我们可以实现绑定多个事件处理程序，但要注意的是同样的事件和事件流机制下相同的方法只会触发一次，即相同的方法会发生覆盖。
等等，这里的事件流又是什么呢？

## 事件流机制

<img src="http://p4a8bakov.bkt.clouddn.com/event1.png" />
&emsp;&emsp;我们已经知道，一个元素绑定了特定事件之后，在事件发生的同时，函数就会被调用，但请看上图这种请看，爷爷，父亲和儿子这三个div元素都同时绑定了各自单击事件`funcgrand(),funcparent(),funcchild()`，那么当我在儿子上单击时，哪个函数会被触发呢？

&emsp;&emsp;首先来分析一下，如果说直观感受是在儿子元素上发生的单击事件，所以应该触发`funcchild()`,但细细想来这样是不妥的，因为儿子元素本身就是父亲元素甚至爷爷元素的一部分，所以说是不是相当于也在父亲和爷爷元素上发生了单击事件呢？答案是是的，这种情况下三个元素绑定的对应事件的函数都会被浏览器触发，那么问题又来啦，既然三个函数都会被触发，那么它们应该以什么顺序被触发呢，是自上到下呢，还是自下到上呢？

&emsp;&emsp;这个问题也就是我们常说的事件流了，即元素从页面中接收事件的顺序，也即事件在页面中的传播顺序。

&emsp;&emsp;W3C对这个问题给了我们一个答案，就是都可以，既可以自上而下依次触发，又可以自下而上触发，具体顺序由我们自己而定(之所以支持这两种方式，是为了与之前浏览器的实现兼容，因为早期IE事件传播方向为从上至下，而Netscape 则从下至上)。

### 事件捕获和事件冒泡

&emsp;&emsp;实际上，之前我们提到的`addEventListener`还有第三个参数，可以为`true`或`false`.当第三个参数为`true`时，绑定的是捕获阶段的事件，在捕获阶段，事件是由上到下依次触发的，反之当第三个参数为`false`时，绑定的是冒泡阶段的事件，在冒泡阶段，事件是由下到上触发的。

&emsp;&emsp;W3C规定，当事件发生时，最先通知window,然后是document,由上到下依次进入知道最底层的被触发的那个元素(也就是目标元素，通常的`event.target`的值)为止，这个过程就是捕获。
&emsp;&emsp;之后，事件会从目标元素开始，冒泡，由下至上逐层传递至window,这个过程就是冒泡。

&emsp;&emsp;所以,捕获是会比冒泡先执行的

### Tips

&emsp;&emsp;正如事件捕获和事件冒泡提到的，事件程序可能会在两个阶段中被执行，即捕获中和冒泡中，当一个事件添加了两个处理函数，一个指定了参数`true`,一个指定的参数`false`,则它们都会被执行，且参数为`true`的那个先执行，因为是捕获阶段先发生.

&emsp;&emsp;但是有一个例外，即如果事件函数被添加在了目标元素本身上，如之前的例题中的儿子元素上被绑定了两个单击事件函数，一个第三个参数是`true`,一个第三个参数是`false`，则它们的实际执行顺序是不受第三个参数控制的，而只是单纯的和添加事件的顺序有关(先`addEventListener`的先执行),这个可能是和处于目标阶段有关(目标阶段和捕获阶段和冒泡阶并称为三大阶段，所以说目标阶段中要把捕获和冒泡的思想排除？真正的顺序是捕获—>目标阶段->冒泡吧?)？


## IE事件处理程序

&emsp;&emsp;对于IE来说，在IE9之前，必须使用`attachEvent`而不是标准方法`addEventListener`,IE事件处理程序中有类似于DOM2级事件处理程序的2个方法`attachEvent`和`detachEvent`

&emsp;&emsp;它们都接收两个参数:
1. 事件处理程序名称,如 `onclick`,`onmounseover`,注意,这里是事件处理程序名称，而不是事件名称，要有前缀`on` 

2. 事件处理程序函数

3. 不像DOM2级事件处理程序一样，它们不接收第三个参数，因为IE8及更早版本只支持冒泡事件流(没有捕获阶段)
 
### Tips
&emsp;&emsp;在IE8中，事件执行的顺序不是添加的顺序而是添加顺序的相反顺序，而在IE6,7中 事件执行的顺序是随机的，和添加顺序无关。

&emsp;&emsp; 使用attachEvent方法还有个缺点是，this的值会变成window对象的引用而不是触发事件的元素。 



## 跨浏览器的事件处理程序

&emsp;&emsp;就像上述提到的，老的IE浏览器的事件处理程序不同于标准的DOM2事件处理，所以为了兼容各浏览器的事件处理,我们可以用一个封装的工具函数来实现通用的添加，移除事件。

```
var EventUtil={
    addEventHandler: function(type,element,handler){
        if(element.addEventListener){
            element.addEventListener(type,handler,false);
        }else if(element.attachEvent){
            element.attachEvent("on"+type,element);
        }else{
            element["on"+type]=handler;
        }
    },
    removeEventHandler: function(type,element,handler){
        if(element.removeEventListener){
            element.removeEventListener(type,handler,false);
        }else if(element.detachEvent){
            element.detachEvent("on"+type,element);
        }else{
            element["on"+type]=null;
        }
    }
}
```

## 事件对象

&emsp;&emsp;事件对象是用来记录一些事件发生时的相关信息的对象，但事件对象只有事件发生时才会产生，并且只能在事件处理函数内部访问，在所有事件处理函数结束后，事件对象会被销毁。

&emsp;&emsp;标准的Event对象属性主要有以下几个：

> 1. bubbles 布尔值,表示事件是否是冒泡类型
> 2. cancelable 布尔值，表示事件是否可以取消默认动作
> 3. currentTarget 当前目标元素，即添加当前事件处理程序的元素
> 4. target 实际目标元素，即实际触发事件的元素
> 5. type 返回当前事件的名称
> 6. eventPhase 事件传播的当前阶段,1表示捕获阶段

&emsp;&emsp;标准的Event对象的方法主要有以下几个：

> 1. preventDefault() 通知浏览器不要执行该事件的默认动作，常用于阻止链接的跳转，表单的提交，等标签的默认行为
> 2. stopPropagation() 冒泡阶段下,阻止事件的继续向上冒泡

### 事件对象的兼容性

&emsp;&emsp;和事件处理程序一样，事件对象的属性和方法也存在兼容性问题。

1. 事件对象的获取
&emsp;&emsp;在IE8及以前的版本中，通过设置属性注册事件处理程序时，调用的时候并未传递事件对象，需要通过全局对象`window.event`来获取,解决方式如下:


```

function getEvent(event){
    event = event || window.event
}
function hander(event){
    event = getEvent(event)
    ...
}

```
2. 阻止默认事件行为

&emsp;&emsp;IE浏览器的`event`事件没有`preventDefault()`这个方法，但是可以通过设置`event`的`returnValue`值为`false`来达到同样的效果，如下:


```

window.event.returnValue=false;

```

3. 阻止冒泡 

&emsp;&emsp;IE浏览器的`event`对象也没有`stopPropagation()`方法，但可以设置`cancelBubble`属性为`true`，阻止事件的继续传播，如下:

```

window.event.cancelBubble=true; 

```


## 事件委托 

&emsp;&emsp;事件委托就是利用事件冒泡，只需指定一个事件处理程序，就可以管理某一类型的所有事件，通过事件委托，可以做到通过在祖先元素添加一个事件处理程序，就可以控制其子孙元素的某些行为。

1. 首先来看利用事件委托来管理某一类型的所有事件的例子。

&emsp;&emsp;需求是未ul下的所有li添加click事件对应的行为处理，在没有用事件委托之前，代码是着这样的: 


```

<ul>
<li>列表项1</li>
<li>列表项2</li>
<li>列表项3</li>
</ul>
<script>
var list=document.getElementsByTagName("li");
for(i=0;i<list.length;i++){
    list[i].onclick=function(){
        alert("我是"+e.target);
    }
}
</script>

```

&emsp;&emsp;目前确实达到了，能够所有li都能对click事件有所响应了，但如果再添加一个添加列表项的按钮呢？当动态的添加列表项时，列表项元素被添加了，可是新添加的节点是没有绑定事件的(除非在添加元素时再加上绑定事件的逻辑)，到这里，我们发现了问题所在:

> 1. 在所有元素上一一添加事件绑定会导致频繁的操作DOM获取元素,同时多个元素各自监听自己的事件，都会增加浏览器的消耗 
> 2. 在页面中动态添加元素时，还需要重新走一遍添加监听事件的逻辑才能使新元素能够响应事件 

&emsp;&emsp;庆幸的是，针对这个问题，我们有更好的解决方案，即利用冒泡的原理实现的事件委托。 

&emsp;&emsp;我们只监听最外层元素，然后在事件处理函数中根据事件源，即`target`属性，进行不同的事件处理，这样，我们只需要针对一个元素添加事件处理程序，极大的降低了DOM访问，并且不需要单独为动态添加的元素添加监听事件了，因为元素的事件会冒泡到最外层，被最外层的事件处理程序截获,如下：

```

var ul=document.getElementById('ulList');
ul.onclick=function(e){
    var e= e || window.event;
    var target = e.target || e.srcElement;
    if(target.nodeName.toLowerCase() === "li"){
        alert("我是"+e.target);
    }
}

```

&emsp;&emsp;从这个例子可以看出，当用事件委托的时候，完全不需要遍历元素的子节点，只需要给父级元素添加事件监听就好了，之后新添加的子节点也能够同样的对触发事件作出适当的响应 

2.  还有一个常见的利用事件委托的例子，就是点开浮层，关闭浮层，我们常常利用事件委托来监听元素外空间区域的点击，来关闭浮层。

### Tips

> 1. 不是所有事件都是可以委托的。适合用事件委托的事件有:`click mousedown mouseup keydown keyup keypresss`





## 例题

1. 选择对的事件处理方式


```

<script>
function sayhi(){
    console.log("hi");
}
</script>
<button id=X onclick="sayhi">A</butotn>//Wrong
<button id=X onclick="sayhi()">B</butotn>//Right
<button id=X onclick="sayhi.call()">C</butotn>//Right

X.onclick=print;//Right
Y.onclick=print();//Wrong
Z.onclick=print.call();//Wrong

```

