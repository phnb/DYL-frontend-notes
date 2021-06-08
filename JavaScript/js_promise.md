# Promise操作
Promise是异步编程的一种解决方案，可以替代传统的解决方案--回调函数和事件，能够很好的解决异步编程中回调地狱（Callback Hell）的问题。ES6统一了用法，并原生提供了Promise对象。作为对象，Promise有一下两个特点：

- 对象的状态不受外界影响。
- 一旦状态改变了就不会在变，也就是说任何时候Promise都只有一种状态。
#### 1.1 Promise的状态
Promise有三种状态，分别是：Pending（进行中）， Resolved(已完成)，Rejected (已失败)。Promise从Pending状态开始，如果成功就转到成功态，并执行resolve回调函数；如果失败就转到失败状态并执行reject回调函数。

#### 1.2 Promise基本用法
首先构造一个promise对象

```JavaScript
let p = new Promise(function(resolve, reject){
	//异步操作
	setTimeout(function(){
		console.log('hello');
    	resolve('not okay');
	}, 2000);
});
```

```JavaScript
hello
```

其执行过程是：执行了一个异步操作，也就是setTimeout，2秒后，输出“执行完成”，并且调用resolve方法。

注意！这里只是new了一个对象，并没有调用它，我们传进去的函数就已经执行了，这是需要注意的一个细节。所以我们用Promise的时候一般是包在一个函数中，在需要的时候去运行这个函数，比如：

```JavaScript
<div onClick={promiseClick}>开始异步请求</div>
 
const promiseClick =()=>{
	console.log('called')
	let p = new Promise(function(resolve, reject){
	//异步操作
	setTimeout(function(){
		    console.log('hello');
		    resolve('not okay');
	    }, 2000);
	});
    return p
}
```

当放在函数里面的时候只有调用的时候才会被执行

那么，接下里解决两个问题：

1、为什么要放在函数里面?

2、resolve是什么?

我们包装好的函数最后，会return出Promise对象，也就是说，执行这个函数我们得到了一个Promise对象。接下来就可以用Promise对象上有then、catch方法了，这就是Promise的强大之处，举例如下：

``` JavaScript
promiseClick().then(function(data){
    console.log(data);
    //后面可以用传过来的数据做些其他操作
    //......
});
```

先是方法被调用起床执行了promise,最后执行了promise的then方法，then方法是一个函数接受一个参数是接受resolve返回的数据这事就输出了‘’

这时候你应该有所领悟了，原来then里面的函数就跟我们平时的回调函数一个意思，能够在promiseClick这个异步任务执行完成之后被执行。这就是Promise的作用了，简单来讲，就是能把原来的回调写法分离出来，在异步操作执行完后，用链式调用的方式执行回调函数。

你可能会觉得在这个和写一个回调函数没有什么区别；那么，如果有多层回调该怎么办？如果callback也是一个异步操作，而且执行完后也需要有相应的回调函数，该怎么办呢？总不能再定义一个callback2，然后给callback传进去吧。而Promise的优势在于，可以在then方法中继续写Promise对象并返回，然后继续调用then来进行回调操作。

所以：精髓在于：Promise只是能够简化层层回调的写法，而实质上，Promise的精髓是“状态”，用维护状态、传递状态的方式来使得回调函数能够及时调用，它比传递callback函数要简单、灵活的多。所以使用Promise的正确场景是这样的：

```JavaScript

promiseClick()
.then(function(data){
    console.log(data);
    return runAsync2();
})
.then(function(data){
    console.log(data);
    return runAsync3();
})
.then(function(data){
    console.log(data);
});
```
这样能够按顺序，每隔两秒输出每个异步回调中的内容，在runAsync2中传给resolve的数据，能在接下来的then方法中拿到。

