---
title: js运行机制：异步和单线程
date: 2019-09-19
tags: [前端,面试题,异步和单线程]
---
面试时，关于`同步和异步`，可能会问以下问题：

+ 同步和异步的区别是什么？分别举一个同步和异步的例子
+ 一个关于setTimeout的笔试题
+ 前端使用异步的场景哪些？

面试时，关于`js运行机制`，需要注意以下几个问题：

+ 如何理解JS的单线程
+ 什么是任务队列
+ 什么时EventLoop
+ 理解哪些语句会放入异步任务队列
+ 理解语句放入异步任务队列
+ 理解语句放入异步任务队列的时机

<!-- more -->

## JS的异步和单线程

> 因为是单线程，所以必须异步。
我们通过题目来解释一下。

**<font
 size=4>题目一：异步</font>**

现有如下代码：

``` 
    console.log(1);
    setTimeout(function () {
        console.log(2);
    }, 1000);
    console.log(3);
    console.log(4);
```

上面的代码中，我们很容易知道，打印的顺序是`1,3,4,2`。因为你会想到，要等一秒之后再打印`2`。

可如果我把延时的时间从`1000`改为`0`：

``` 
    console.log(1);
    setTimeout(function () {
        console.log(2);
    }, 0);
    console.log(3);
    console.log(4);
```

上方代码中，打印的顺序仍然是`1,3,4,2`。这是为什么呢？我们来分析一下。

**<font
 size=4 color=orange>总结</font>**

js是单线程（同一时间只能做一件事），而且有一个<font color=red>任务队列</font>；全部的同步任务执行完毕后，再来执行异步任务。第一行代码和最后一行代码是同步任务；但是，`setTimeout`是<font color=red>异步任务</font>。

于是，执行的顺序是：

+ 先执行同步任务`console.log(1)`
+ 遇到异步任务`setTimeout`,要<font color=red>挂起</font>
+ 执行同步任务`console.log(3)`
+ <font color=red>全部的同步任务执行完毕后，再来执行异步任务</font>`console.log(2)`.

很多人会把这个题目答错，这是因为它们不懂js的运行机制。

注意上面那句话：同步任务执行完毕后，再来执行异步任务。也就是说，<font color=red>如果同步任务没有执行完，异步任务是不会执行的</font>。为了解释这句话，我们来看下面这个例子。

**<font size=4>题目二：异步</font>**

现有如下代码：

``` 
    console.log('A');
    while (1) {

    }
    console.log('B');
```

我们很容易想到，上方代码的打印结果是`A`,因为while是同步任务，代码会陷入死循环里出不来，自然也就无法打印`B`。

可如果我把代码改成下面的样子：

``` 
    console.log('A');

    setTimeout(function () {
        console.log('B');
    })

    while (1) {

    }
```

上方代码的打印结果仍然是`A`。因为while是同步任务，setTimeout是异步任务，所以还是那句话：<font color=red>如果同步任务没有执行完，队列里的异步任务是不执行的</font>。

**<font size=4>题目三：同步</font>**

```
    console.log('A');

    alert('haha'); //1秒之后点击确认

    console.log('B');
```

`alert`函数是同步任务，我只有点击了确认，才会继续打印`B`。

**<font size=4>同步和异步的对比</font>**

现在上面列举了异步和同步的例子。现在描述一下区别：【<font color=red>重要</font>】

因为`setTimeout`是<font color=red>异步任务</font>，所以程序并不会卡在那里，而是继续向下执行（即使setTimeout设置了倒计时一万秒）；但是`alert`函数是<font color=red>同步任务</font>，程序会<font color=red>卡在那里</font>，如果它没有执行，后面的也不会执行（卡在那里，自然也就造成了<font color=red>阻塞</font>）。

**<font size=4>前端使用异步的场景</font>**

什么时候需要<font color=red>等待</font>，就什么时候用异步。

+ 定时任务：setTimeout（定时炸弹）、setInterval（循环执行）
+ 网络请求：ajax请求、动态`<img>`加载
+ 时间绑定（比如说，按钮绑定点击事件之后，用户爱点不点。我们不可能卡在按钮那里，所以不做。所以，应该用异步）
+ ES6中的Promise

代码举例：

``` 
    console.log('start');
    var img = document.createElement('img');
    img.onload = function () {
        console.log('loaded');
    }
    img.src = '/xxx.png';
    console.log('end');
```

上图中，先打印`start`，然后执行`img.src = '/xxx.png'`，然后打印`end`，最后打印`loaded`。

## 任务队列和Event Loop（事件循环）

**<font size=4>任务队列</font>**

所有任务可以分成两种，一种是同步任务（synchronous），另一种是异步任务（asynchronous）。同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务。异步任务指的是，不进入主线程、而进入“任务队列”（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

<font size=4 color=orange>总结</font>：<font color=red>只要主线程空了，就会去读取“任务队列”，这就是JavaScript的运行机制</font>。【<font color=red>重要</font>】

Event Loop

主线程从“任务队列”中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为Event Loop（事件循环）。

![687474703a2f2f696d672e736d79687661652e636f6d2f32303138303331305f313834302e706e67.png](https://i.loli.net/2019/09/19/IEMqpzh2OmdGSKL.png)

在理解Event Loop时，要理解两句话：

+ 理解哪些语句会放入异步任务队列
+ 理解语句放入异步任务队列的<font color=red>时机</font>

**<font size=4>容易答错的题目</font>**

``` 
    for (var i = 0; i < 3; i++) {
        setTimeout(function () {
            console.log(i);
        }, 1000);
    }
```

很多人以为上面的题目，答案是`0,1,2,3`。其实，正确的答案是：`3,3,3,3`。

分析：for循环是同步任务，setTimeout是异步任务。for循环每次遍历的时候，遇到setTimeout，就先暂留着，等同步任务全部执行完毕（此时，i已经等于3了），再执行异步任务。

我们把上面的题目再加一行代码。最终代码如下：

``` 
    for (var i = 0; i < 3; i++) {
        setTimeout(function () {
            console.log(i);
        }, 1000);
    }
    console.log(i);
```

如果我们约定，用箭头表示其前后的两次输出之间有1秒的时间间隔，而逗号表示其前后的两次输出之间的时间间隔可以忽略，代码实际运行的结果该如何描述？可能会有两种答案：

+ A.60%的人会描述为：`3 -> 3 -> 3 - > 3`，即每个3之间都有1秒的时间间隔；
+ B.40%的人会描述为：`3 -> 3，3，3`，即1个3直接输出，1秒之后，连续输出3个3。

循环执行过程中，几乎同时设置了3个定时器，这些定时器都会在1秒之后触发，而循环完的输出是立即执行的，显而易见，正确的描述是B。

上面这个题目的参考链接：

+ [80%应聘者都不及格的JS面试题](https://juejin.im/post/58cf180b0ce4630057d6727c)
+ [深入浅出JavaScript时间循环机制（上）](https://zhuanlan.zhihu.com/p/26229293)