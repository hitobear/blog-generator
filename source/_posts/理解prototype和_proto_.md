---
title: 理解prototype和__proto__
---
prototype和__proto__时对象属性的访问过程中至关重要的两个属性，这里主要对这两个属性的特点和意义作一些总结。
# 从对象的属性说起
首先，**假设不存在prototype和__proto__**,我们知道JS的是存在Object类型的，和很多面向对象语言一下，类型是对多个同种类对象的封装，如学生A存在年龄，姓名，性别，父母，学校，班级,toString,valueOf等属性，和起床，跑步，上学，下课四个方法，学生B，C同样存在这些属性和方法，为了统一管理这些学生的属性和行为，我们可以提供一个Student类，Student类中包含了所有的这些属性和方法，这是通过Student类就可以创建多个学生对象，假设创建了学生a,b,c,现在内存中a,b,c对象的构成是下图1所示的样子：
<div align="center"><img src="http://p4a8bakov.bkt.clouddn.com/objectorigin.png"/></div>
<div align="center">图1</div>

##  多个同类对象的问题
由图1可以看到，在这种未经过任何处理的存储结果下，a,b,c三个学生的属性在内存中确实可以完整的存储和访问，但是稍加思考就能觉得好像哪里不太对，是的，有一些属性实在每个学生里都出现的，比如图中可以看到的`run(),study(),gotoschool()`以及图中没有画出的`toString(),valueOf()`方法，这么多同样的属性，实际上是没有任何区别的，那位什么要在每一块内存里都存一遍呢？现在只是三个对象，如果会新建成百上千个这类对象呢，空间浪费岂不大发了？这里为了践行不浪费一粒空间的宗旨，前辈们想到了**共享属性**,如何让这些没有任何区别的属性内在不同对象之间共享，那就好了！于是就有了以下的图2：
<div align="center"><img src="http://p4a8bakov.bkt.clouddn.com/objectproto.png"/></div>
<div align="center">图2</div>
可以看到，在这种结构中，a,b,c三个对象中的属性值相同的几个属性均消失了，代替的是多了一个名为`__proto__`的属性,图中可以看出，`__proto__`是一个对象，这个对象所包含的属性正是学生类对象的共享属性，通过把这几个共享的属性放到同一块内存中构成一个对象p，并且通过新增`__proto__`属性的方式，使得对象可以引用到这个对象p,就实现了*通过引用同样的__prop__属性来共享属性对象*

## 访问共享属性的钩子——__proto__
前面已经提到了，通过__proto__对象中可以访问到同类对象的共享属性，比如对象a中并没有直接包含run()方法，但是a.__proto__中包含run()方法，这样当用到a.run()时，js引擎在a的直接属性中未找到该方法，则自动会去a.__proto__中寻找该方法属性，如果依然找不到该方法，则继续去下一层a.__proto__.__proto__寻找该方法，直到某一层的__proto__未undefined为止，利用这个特性，完全可以对一个对象做多层抽象到多个__proto__中，如上例中的学生类的共享属性中包含run(),study(),gotoschool()，toString(),valueOf()方法，完全可以把run(),toString(),valueOf()抽象成一个共享属性对象，为People类使用，因为这些方法属性都是人类的共有特性，进而再把toString(),valueOf()抽象成一个共享属性对象，为Object类使用，即所有的对象所共有的特性。

### __proto__属性是所有对象与生具来的属性
我们已经知道，通过__proto__属性可以访问到对象所属类的共享属性，那么这个属性到底来自哪里？总不能每次新建一个对象的时候都要我们手动为__proto__属性来赋值吧？不用担心，来看一下就好了~
创建对象的方式有两种，一种是直接用对象字面量来创建，一种是用构造函数法来创建，我们分别看一下这两种创建方式创建的对象的属性
<div align="center"><img src="http://p4a8bakov.bkt.clouddn.com/image/2018/02/24__proto1.png"/></div>
<div align="center">图3 字面量创建法</div>
<div align="center"><img src="http://p4a8bakov.bkt.clouddn.com/image/2018/02/24__proto2.png"/></div>
<div align="center">图4 构造函数创建法</div>
图三图四可以看出，无论是哪种方式创建的对象，都自带__proto__属性，也就是说一个对象相关的共享属性对象根本无需我们手动指定，它被JS引擎自动赋值给了这个对象的__proto__属性。

## 共享属性对象的直接生产者——prototype

目前为止，我们知道，通过一个对象的__proto__属性可以访问到它相关的共享属性对象，那么这个所谓的共享对象到底是什么呢？我们还不得而知。同样的，针对两种方式创建的对象的分别查看一下他们的__proto__属性的详细信息
<div align="center"><img src="http://p4a8bakov.bkt.clouddn.com/image/2018/02/24__proto3.png"/></div>
<div align="center">图5 __proto__详细信息——字面量创建法</div>
<div align="center"><img src="http://p4a8bakov.bkt.clouddn.com/image/2018/02/24__proto4.png"/></div>
<div align="center">图6 __proto__详细信息——构造函数创建法</div>
通过图5，图6的对比可以看出，两种创建方式的__proto__属性好像差距有点大，这是问什么呢？仔细看看，用对象字面量方式创建的对象中的该属性看上去似曾相识？可以在图5中看到，__proto__属性包含toString(),valueOf(),isPropertyOf(),hasOwnProperty()等一系列Object对象的原生方法，那么这里的__proto__属性何Object是不是有什么关系呢？所以~是时候来和Object.prototype属性作一下对比了
```
//对象字面量方式创建的对象
var a={name:'lily',age:17};
typeof Object;//"function"
console.log(a.__proto__===Object.prototype);//true
//构造方式创建的对象
var Test=function(){};
typeof Test;//"function"
var b=new Test();
console.log(a.__proto__===Object.prototype);//false
console.log(a.__proto__===Test.prototype);//true
```
首先，从对象字面量方式创建对象的执行的结果来看，对象的__proto__属性是等于Object的prototype属性的，但是在构造方式创建的对象中这个等式却是不成立的，是不是代表__proto__属性和prototype属性没什么必然关系了呢？NoNoNo!继续往下看 `console.log(a.__proto__===Test.prototype)`这个结果是`true`的，表示a对象的__proto__和Test的prototype属性建立了`等于`关系,那这又说明了什么呢？
很简单，通过递归可以得出 `o.__proto__===Func.prototype`这个恒等式，其中`o`表示任何方式创建的一个对象，`Func`表示创建这个对象的构造函数,现在是不是可以理解了，该例中b是通过构造函数Test创建的，所以b.__proto__===Test.prototype,而a是字面量对象，字面量对象实际上是JS引擎由Object构造的，所以当然了，a.__proto===Object.prototype

到现在，可以推出**函数的`prototype`属性实际上才是真正存放共享属性对象的宿主**，关于prototype属性有以下特点：
* JS引擎为**所有函数**默认生成了prototype属性
* prototype属性的值是一个对象
* 默认的prototype属性对象都自带Constructor属性，且该属性的值是函数本身(注意重新为prototype赋值后Constructor可能不复存在)。

## __proto__和prototype的不同意义

我们知道，一般情况下，prototype是函数所特有的属性，而__proto__是所有对象都有的属性，但是函数也是对象，所以就有了：默认情况下，所有函数都同时存在__proto__和prototype属性，但是这两个属性的意义是不同的(__proto__表示了创建该函数的函数(一般是Function)提供的共享属性(即Function.prototype),而prototype则是为通过该函数本身创建的其它对象提供共享属性，通常这个属性会被人为覆盖或扩展)，所以一般有`函数.__proto__!==函数.prototype`

## 特殊的函数——Function
刚才说到，一般有`函数.__proto__!==函数.prototype`，但实际上有一个特殊情况，即Function对象本身，它也是一个函数，并且它是创建构造函数的函数，可以很容易的验证
`Function.__proto__===Function.prototype`,这个特殊情况不妨这样理解：
Function是所有函数的构造函数，所以肯定存在`Function.prototype`，反过来因为所有函数的构造函数都是Function，所以Function作为函数也不例外，它也应该能理解为通过Function构造函数创建的，所以又Function.__proto__===Function.prototype
## 总结
现在，可以对__proto__和prototype来个一句话总结了~——函数的prototype和 对象的__proto__属性共同配合，才有了对象的原型链机制，其中__proto__属性是我们层层递归的访问抽象类的共享属性的直接钩子(没有这个属性，JS引擎无法查找一个对象的上一层的共享属性)，而prototype则使得对象能够自动和共享属性对象绑定成为可能(无需我们手动为__proto__属性赋值，通过JS引擎自动将对象的属性__proto__  *和创建对象的构造函数的prototype相绑定*)

