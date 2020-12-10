# Node环境下的Event Loop

看看下面这段代码的执行结果是什么？

```javascript
console.log('1')

setTimeout(function callback(){
 console.log('2')
}, 1000)

new Promise((resolve, reject) => {
    console.log('3')
    resolve()
})
.then(res => {
    console.log('4');
})

console.log('5')
```

很明显这道题考察的就是你对Event Loop的认识，不出所料，我当时就没做对这道题，但我默默得记下了这个考题，现在学习后回来整理知识点

你们知道正确答案是什么吗？这里先埋下个伏笔，大家可以自己做一做这道题，答案会在文章中公布

# 一、JavaScript是如何工作的

在刚开始学习JavaScript时，你一定听过这样一句话：**JavaScript是单线程的**

什么是单线程呢？就是很多段JS代码，它的执行顺序是从上到下一行一行执行的，即只有当上一行的代码执行完后才会执行下一行代码

这样的设定也是为了保证我们在实现某些功能时的代码逻辑的顺序性

此时有些人就会提出问题，上来就甩了一段代码给我，代码如下

```javascript
console.log('1')

setTimeout(function (){
 console.log('2')
}, 1000)

console.log('3')

/* 运行结果：
 1
 3
 2
*/
```

不是说JS是单线程的，一行一行代码执行的吗？为什么这段代码先打印了 `3` ，再打印了 `2`呢？

先给出一个知识点，在JS中有些代码是**异步**执行的，所谓异步，就是不会阻塞代码的运行，而会另外开启一个空间去执行这段异步代码，其余同步的代码就仍正常执行，若异步代码中有其它的代码，则会在之后的**某个时刻**将异步代码中其它代码执行。例如上述代码中的 `setTimeout` 函数就是异步的，而其内部还有一段同步代码 `console.log('2')`

这里提到的某个时刻，也正是我们本文后续要讲到的重点，这里就先不做过多讲解

那么异步执行的额外空间是哪里来的？那当然是JS所处的运行环境提供的了，而JS最主要的两个运行环境就是：**浏览器** 和 **Node**，我们接下来也会基于这两个运行环境，对JS的运行机制进行讲解

# 二、浏览器中的JavaScript

之所以JS能在浏览器中运行，那是因为浏览器都默认提供了一个JavaScript引擎，为JS提供一个运行环境

对于内存堆我们就不做过多的了解，主要讲一下调用栈

## （1）调用栈

什么是调用栈？这里有一段代码，我们通过它来分析一下调用栈的运行过程

```javascript
function multiply(a, b) {
 return a * b
}

function calculate(n) {
 return multiply(n, n)
}

function print(n) {
 let res = calculate(n)
 console.log(res)
}

print(5)
```

当这段代码在浏览器中运行时，会先查询三个定义好了的函数 `multiply` 、`calculate` 和 `print` ；然后执行 `print(5)` 这段代码，因为这三个函数是有调用关系的，因此接下来依次调用了 `calculate` 函数 、`multiply` 函数

现在，我们来看一下这段代码在执行过程中，调用栈stack内部的情况如何

![img](http://my.flytodream.cn/docImgage/29292929.png)这里，还有一种方式可以来验证一下调用栈的存在以及其内容，我们来编写一段这样的代码：

```javascript
function fn() {
    throw new Error('isErr')
}

function foo() {
    fn()
}

function main() {
    foo()
}

main()
```

然后在浏览器中运行一下，就会得到如下结果：

![img](http://my.flytodream.cn/docImgage/12329320.jpg)

在代码运行过程中抛出错误时，浏览器将整个调用栈里的内容都打印了出来，正如我们所期望的一样，此时的调用栈是这个样子的：

![img](http://my.flytodream.cn/docImgage/5485478538443.jpg)以上的过程涉及到的都是同步的代码，那么对于异步的代码来说，是如何像我们上面所说的一样，开辟一个新的空间去给异步代码运行的呢？

这里就要引入 **Event Loop** 的概念了

## （2）Event Loop

**Event Loop** 翻译过来叫做事件循环，那到底是什么事件在循环呢？这里我们给出完整的浏览器的事件循环简图，来看一下

![img](http://my.flytodream.cn/docImgage/98987892783.jpg)浏览器中的各种 **Web API** 为异步的代码提供了一个单独的运行空间，当异步的代码运行完毕以后，会将代码中的回调送入到 **Task Queue**（任务队列）中去，等到调用栈空时，再将队列中的回调函数压入调用栈中执行，等到栈空以及任务队列也为空时，调用栈仍然会不断检测任务队列中是否有代码需要执行，这一过程就是完整的Event Loop 了

我们可以用一个简单的例子，来感受一下事件循环的过程

```javascript
console.log('1')

setTimeout(function callback(){
 console.log('2')
}, 1000)

console.log('3')
```

再通过动图来看看大致的过程

![img](http://my.flytodream.cn/docImgage/53453423423423.jpg)在这里插入图片描述

## （3）宏任务和微任务

简单理解了 Event Loop 的过程后，我们再来看一道题，看看是否能回答正确

```javascript
console.log('1')

setTimeout(function callback(){
 console.log('2')
}, 1000)

new Promise((resolve, reject) => {
    console.log('3')
    resolve()
})
.then(res => {
    console.log('4');
})

console.log('5')
// 这段代码的打印结果顺序如何呢？
```

下面公布一下答案

```
// 正确答案:
   1
   3
   5
   4
   2
```

这里你是否又有个疑问了，为什么 `promise` 和 `setTimeout` 同样是异步，为什么前者优先于后者？

这里就要引入另外两个概念了，即 **macrotask**（宏任务） 和 **microtask**（微任务）

下面列举了我们浏览器中常用的宏任务和微任务

| 名称   | 举例（常用）                            |
| :----- | :-------------------------------------- |
| 宏任务 | setTimeout 、setInterval 、UI rendering |
| 微任务 | promise 、requestAnimationFrame         |

并且规定，当宏任务和微任务都处于 Task Queue 中时，微任务的优先级大于宏任务，即先将微任务执行完，再执行宏任务

因此，上述代码先打印了 `4` ，再打印了 `2`

当然，既然区分了宏任务和微任务，那么存放它们的队列也就分为两种，分别为**macro task queue**（宏队列） 和 **micro task queue**（微队列），如图所示

![img](http://my.flytodream.cn/docImgage/435345345.jpg)根据相关规定，当调用栈为空时，对于这两个队列的检测情况步骤如下：

1. 检测微队列是否为空，若不为空，则取出一个微任务入栈执行，然后执行步骤1；若为空，则执行步骤2
2. 检测宏队列是否为空，若不为空，则取出一个宏任务入栈执行，然后执行步骤1；若为空，直接执行步骤1
3. ……往复循环

那么我们来看一下刚才那段代码的具体调用过程吧（由于wx对动图的限制，我不得不把动图分成两部分，还请大家耐心观看，十分抱歉）



![img](http://my.flytodream.cn/docImgage/53453454.gif)



![img](http://my.flytodream.cn/docImgage/5789347583485.jpg)



看完这段执行过程，再去写一下上面那道题，看看能否写对呢？

# 三、Node.js中的JavaScript

**注：** 此次讨论的都是针对Node.js 11.x以上的版本

本文分别讨论了JS在浏览器环境和Node.js环境这两种情况，那自然是有所区别的，后者相对于前者的过程分得更加细致

## （1）node中的Event Loop

我们来看一张Node.js的 Event Loop 简图

![img](http://my.flytodream.cn/docImgage/342342543.jpg)Node.js的Event Loop 是基于libuv实现的

通过 Node.js 的官方文档可以得知，其事件循环的顺序分为以下六个阶段，每个阶段都会处理专门的任务：

- **timers：** 计时器阶段，用于处理setTimeout以及setInterval的回调函数
- **pending callbacks：** 用于执行某些系统操作的回调，例如TCP错误
- **idle, prepare：** Node内部使用，不用做过多的了解
- **poll：** 轮询阶段，执行队列中的 I/O 队列，并检查定时器是否到时
- **check：** 执行setImmediate的回调
- **close callbacks：** 处理关闭的回调，例如 socket.destroy()

以上六个阶段，我们需要重点关注的只有四个，分别是 **timers** 、**poll** 、**check** 、**close callbacks**

这四个阶段都有各自的宏队列，只有当本阶段的宏队列中的任务处理完以后，才会进入下一个阶段。在执行的过程中会不断检测微队列中是否存在待执行任务，若存在，则执行微队列中的任务，等到微队列为空了，再执行宏队列中的任务（这一点与浏览器非常类似，但在Node 11.x版本之前，并不是这样的运行机制，而是运行完当前阶段队列中的所有宏任务以后才会去检测微队列。对于11.x 之后的版本，虽然在官网我还没找到相关文字说明是这样的，但通过无数次的运行，暂且可以说是这样的，若各位找到相关的说明，可以留下评论）

同理，Node.js也有宏任务和微任务之分，我们来看一下常用的都有哪些

| 名称   | 举例（常用）                            |
| :----- | :-------------------------------------- |
| 宏任务 | setTimeout 、setInterval 、setImmediate |
| 微任务 | Promise 、process.nextTick              |

可以看到，在Node.js对比浏览器多了两个任务，分别是宏任务 `setImmediate` 和 微任务 `process.nextTick`

`setImmediate` 会在 check 阶段被处理

`process.nextTick` 是Node.js中一个特殊的微任务，因此会为它单独提供一个队列，称为 **next tick queue**，并且其优先级大于其它的微任务，即若同时存在 `process.nextTick` 和`promise`，则会先执行前者

总结一下，Node.js在事件循环中涉及到了4个宏队列和2个微队列，如图所示

![img](http://my.flytodream.cn/docImgage/272834627834.jpg)在了解了基本过程以后，我们先来写一道简单的题

```javascript
setTimeout(() => {
    console.log(1);
}, 0)

setImmediate(() => {
    console.log(2);
})

new Promise(resolve => {
    console.log(3);
    resolve()
    console.log(4);
})
.then(() => {
    console.log(5);
})

console.log(6);

process.nextTick(() => {
    console.log(7);
})

console.log(8);

/* 打印结果：
   3
   4
   6
   8
   7
   5
   1
   2
*/
```

首先毫无疑问，同步的代码一定是最先打印的，因此先打印的分别是 `3 4 6 8`

再来判断一下异步的代码，`setTimeout` 被送入 `timers queue` ；`setImmediate` 被送入 `check queue` ；`then()` 被送入 `other microtask queue` ；`process.nextTick` 被送入 `next tick queue`

然后我们按照上面图中的流程，首先检测到微队列中有待执行任务，并且我们说过，`next tick queue` 的优先级高于 `other microtask queue`，因此先打印了 `7`，然后打印了 `5` ；到此为止微队列中的任务都被执行完了，接着就进入 `timers queue` 中阶段，所以打印了 `1`，当前阶段的队列为空了，按照顺序进入 `poll` 阶段，但发现队列为空，所以进入了 `check` 阶段，上面说过了这个阶段是专门处理 `setImmediate` 的，因此最后就打印了 `2`

## （2）setTimeout和setImmediate

不知刚才讲了那么多，大家有没有发现，一个循环中，`timers` 阶段是先于 `check` 阶段的，那么是不是就意味着 `setTimeout` 就一定比 `setImmediate` 先执行呢？我们来看个例子

```javascript
setTimeout(() => {
    console.log('setTimeout');
}, 0)

setImmediate(() => {
    console.log('setImmediate');
})
```

我们用node运行该段代码多次，发现得到了如下两种结果：

```javascript
// 第一种结果
setTimeout
setImmediate

// 第二种结果
setImmediate
setTimeout
```

这是为什么呢？

这里我们给 `setTimeout` 设置的延迟时间是 0，表面上看上去好像是没有延迟，但其实运行起来延迟时间是大于0的

然后node开启一个事件循环是需要一定时间的。假设node开启事件循环需要2毫秒，然后 `setTimeout` 实际运行的延迟时间是10毫秒，即事件循环开始得比 `setTimeout` 早，那么在第一轮事件循环运行到 `timers` 时，发现并没有 `setTimeout` 的回调需要执行，因此就进入了下一阶段，尽管此时 `setTimeout` 的延迟时间到了，但它只能在下一轮循环时被执行了，所以本次事件循环就先打印了 `setImmediate`，然后在下一次循环时打印了 `setTimeout`。

这就是刚才第二种结果出现的原因

那么为何存在第一种情况也就更好理解了，那就是 `setTimeout` 的实际的延迟事件小于node事件循环的开启事件，所以能在第一轮循环中被执行

了解了为何出现上述原因以后，这里提出两个问题：

1. 如何能做到一定先打印 `setTimeout` ，后打印 `setImmediate`
2. 如何能做到一定先打印 `setImmediate` ，后打印 `setTimeout`

这里我们来分别实现一下这两个需求

**实现一：**

既然要让 `setTimeout` 先打印，那么就让它在第一轮循环时就被执行，那么我们只需要让事件循环开启的事件晚一点就好了。所以可以写一段同步的代码，让同步的代码执行事件长一点，然后就可以保证在进入 `timers` 阶段时，`setTimeout` 的回调已被送入 `timers queue`

```javascript
setTimeout(() => {
    console.log('setTimeout');
}, 0)

setImmediate(() => {
    console.log('setImmediate');
})

let start = Date.now()
// 让同步的代码运行30毫秒
while(Date.now() - start < 30)
```

多次运行代码发现，每次都是先打印了 `setTimeout`，然后才打印的 `setImmediate`

**实现二：**

既然要让 `setTimeout` 后打印，那么就要想办法让它在第二轮循环时被执行，那么我们可以让`setTimeout` 在第一轮事件循环跳过 `timers` 阶段后执行

刚开始我们讲过，`poll` 阶段是为了处理各种 I/O 事件的，例如文件的读取就属于 I/O 事件，所以我们可以把 `setTimeout` 和 `setImmediate` 的代码放在一个文件读取操作的回调内，这样在第一轮循环到达 `poll` 阶段时，会将 `setTimeout` 送入 `timers queue`，但此时早已跳过了`timers` 阶段，所以其只会在下一轮循环时被打印 ；同时 `setImmediate` 此时被送入了 `check queue` ，那么在离开 `poll` 阶段以后就可以顺利得先打印 `setImmediate` 了

```javascript
const fs = require('fs');

fs.readFile(__filename, () => {
  setTimeout(() => {
    console.log('setTimeout');
  }, 0);
  setImmediate(() => {
    console.log('setImmediate');
  });
});
```

多次运行代码发现，每次都是先打印了 `setImmediate`，然后才打印的 `setTimeout`