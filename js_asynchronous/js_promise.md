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
