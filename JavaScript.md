# JavaScript

## JS语言特性

[JavaScript：彻底理解同步、异步和事件循环(Event Loop) - SegmentFault 思否](https://segmentfault.com/a/1190000004322358)

JS是一个异步语言，我们常说”JavaScript是单线程的”。

所谓单线程是指，在JS引擎中负责解释和执行JS代码的线程只有一个，也叫做主线程。

但是实际上还存在着其他的线程，例如ajax请求的线程，处理DOM事件的线程， 定时器线程，读写文件的线程，（例如在Node.js）中，等等。这些线程可能处于JS引擎之内，也可能处于JS引擎之外，这些线程叫做工作线程。



## 同步与异步

假设存在一个函数function a(args...)

> 同步：如果在函数A返回的时候，调用者就已经可以得到预期的结果（拿到了预期的数值或者产生了预期的效果），那么这个函数就是同步的。

```javascript
Math.sqrt(2)
console.log("hello")
```

- 第一个函数返回的时候，就拿到了预期的返回值，根号2
- 第二个函数返回的时候，产生了预期的效果，在控制台输出了hello

所以这两个函数都是同步函数

> 异步：如果函数A返回的时候，调用者还不能获取到预期的结果，而是需要将来通过一定的手段再得到，那么这个函数是异步函数。

```javascript
fs.readFile('foo.txt', 'utf8', function(err, data) {
    console.log(data);
});
```

在上面的代码中，我们希望通过`fs.readFile`函数读取文件foo.txt中的内容，并打印出来。
但是在`fs.readFile`函数返回时，我们期望的结果并不会发生，而是要等到文件全部读取完成之后。如果文件很大的话可能要很长时间。



正是由于JS是异步的，而异步容易实现非阻塞，所以在JavaScript中对于耗时的操作或者时间不确定的操作使用异步就成了必然选择。



## 异步过程的构成要素

异步函数结束之后，我们还有很多工作要做，例如：工作线程执行异步任务，通知主线程，主线程调用回调函数...我们把整个过程叫做异步过程。

一般来说，一个典型的异步函数通常具有以下的形式

```javascript
A(args, callbackFn)
```

可以叫做异步过程的发起函数，或者叫做异步任务注册函数。args是这个函数的重要的参数，callbackFn也是这个函数的参数，但是比较特殊所以列出来。

所以，从主线程的角度看一个异步任务，包含了两个元素：

1. 发起函数（或者叫注册函数）
2. 回调函数

举个例子：

```javascript
setTimeout(fn, 1000)
```

setTimeout就是异步过程的发起函数，fn就是回调函数

又或者

```javascript
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = xxx; // 添加回调函数
xhr.open('GET', url);
xhr.send(); // 发起函数
```

发起函数和回调函数分离。



## 消息队列和事件循环

上文讲到，异步过程中工作线程在异步操作完成之后通知主线程，通知机制主要采用消息队列或者时间循环。

消息队列：消息队列是一个先进先出的队列，里面存放着各种信息

事件循环：事件循环是指主线程重复从消息队列中取消息、执行的过程

实际上，主线程只会做一件事情，就是从消息队列里面取消息、执行消息，再取消息、再执行。当消息队列为空时，就会等待直到消息队列变成非空。而且主线程只有在将当前的消息执行完成后，才会去取下一个消息。这种机制就叫做事件循环机制，取一个消息并执行的过程叫做一次循环。

```javascript
while(true) {
    var message = queue.get();
    execute(message);
}
```

再次以异步AJAX为例，假设存在如下的代码：

```javascript
$.ajax('http://segmentfault.com', function(resp) {
    console.log('我是响应：', resp);
});

// 其他代码
...
...
...
```

主线程在发起AJAX请求后，会继续执行其他代码。AJAX线程负责请求segmentfault.com，拿到响应后，它会把响应封装成一个JavaScript对象，然后构造一条消息：

```javascript
// 消息队列中的消息就长这个样子
var message = function () {
    callbackFn(response);
}
```

其中的`callbackFn`就是前面代码中得到成功响应时的回调函数。

主线程在执行完当前循环中的所有代码后，就会到消息队列取出这条消息(也就是`message`函数)，并执行它。到此为止，就完成了工作线程对主线程的`通知`，回调函数也就得到了执行。如果一开始主线程就没有提供回调函数，AJAX线程在收到HTTP响应后，也就没必要通知主线程，从而也没必要往消息队列放消息。



## Promise

[理解JavaScript Promise - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/26523836)



## async/await





