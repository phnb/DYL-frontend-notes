# Javascript 异步操作(promise)

## 1. 同步和异步
#### 1.1 同步
如果在函数返回的时候，调用者就能够得到预期结果(即拿到了预期的返回值或者看到了预期的效果)，那么这个函数就是同步的。 

```Javascript
//两个同步的函数
Math.sqrt(2);   //在函数返回时，获得了预期值，即2的平方根
console.log('hello');    //在函数返回时，获得了预期的效果，打印了成功
```

如果两个函数是同步的，即使第一个比较耗时，第二个也会等待第一个完成再开始运行。       <font color = "red">    <- big problem</font>

#### 1.2 异步
如果在函数返回的时候，调用者还不能够得到预期结果，而是需要在将来通过一定的手段得到，那么这个函数就是异步的。
```Javascript
//读取文件
fs.readFile('hello.txt', 'utf8', function(err, data) {
    console.log(data);
});
//网络请求
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = xxx; // 添加回调函数
xhr.open('GET', url);
xhr.send(); // 发起函数
```
上述示例中读取文件函数 readFile和网络请求的发起函数  send都将执行耗时操作，虽然函数会立即返回，但是不能立刻获取预期的结果，因为耗时操作交给其他线程执行，暂时获取不到预期结果（后面介绍）。而在JavaScript中通过回调函数 function(err, data) { console.log(data); }和 onreadystatechange ，<font color = "grey">在耗时操作执行完成后把相应的结果信息传递给回调函数，通知执行JavaScript代码的线程执行回调。 </font>

###### 如果函数是异步的，发出调用之后，马上返回，但是不会马上返回预期结果。调用者不必主动等待，当被调用者得到结果之后会通过回调函数主动通知调用者。
<br>

## 2. 单线程和多线程
> <br>JavaScript是单线程，如果还存在异步，那些耗时操作在哪执行？
<br>

JavaScript只是一门语言，说是单线程还是多线程得结合具体运行环境。JS的运行通常是在浏览器中进行的，具体由JS引擎去解析和运行.

#### 2.1 浏览器
目前最为流行的浏览器为：Chrome，IE，Safari，FireFox，Opera。浏览器的内核是多线程的。

一个浏览器通常由以下几个常驻的线程：
- 渲染引擎线程：负责页面的渲染
- JS引擎线程：负责JS的解析和执行
- 定时触发器线程：处理定时事件，比如setTimeout, setInterval
- 事件触发线程：处理DOM事件
- 异步http请求线程：处理http请求

需要注意的是，渲染线程和JS引擎线程是不能同时进行的。渲染线程在执行任务的时候，JS引擎线程会被挂起。因为JS可以操作DOM，如果在渲染中js处理了dom,会引发混乱。

#### 2.1.1 JS 引擎
通常提到的两个引擎是渲染引擎和JS引擎。渲染引擎就是如何渲染页面，Chrome／Safari／Opera用的是Webkit引擎，IE用的是Trident引擎，FireFox用的是Gecko引擎。不同的引擎对同一个样式的实现不一致，就导致了经常被人诟病的<font color = "red">浏览器样式兼容性问题</font>。

不同浏览器的JS引擎也各不相同，Chrome用的是V8，FireFox用的是SpiderMonkey，Safari用的是JavaScriptCore，IE用的是Chakra。

我们说JavaScript是单线程，就是因为浏览器在运行时只开启了一个JS引擎线程来解析和执行JS。

###### 但是，虽然JavaScript是单线程的，可是浏览器内部不是单线程的。一些I/O操作、定时器的计时和事件监听（click, keydown...）等都是由浏览器提供的其他线程来完成的。
<br>

## 3. 消息队列和事件循环
消息队列与事件循环控制回调函数在JS引擎线程中执行时间以及执行顺序。

- 左边的栈存储的是同步任务，就是那些能立即执行、不耗时的任务，如变量和函数的初始化、事件的绑定等等那些不需要回调函数的操作都可归为这一类。

- 右边的堆用来存储声明的变量、对象。下面的队列就是消息队列，一旦某个异步任务有了响应就会被推入队列中。如用户的点击事件、浏览器收到服务的响应和setTimeout中待执行的事件，每个异步任务都和回调函数相关联。

JS引擎线程用来执行栈中的同步任务，当所有同步任务执行完毕后，栈被清空，然后读取消息队列中的一个待处理任务，并把相关回调函数压入栈中，单线程开始执行新的同步任务。

JS引擎线程从消息队列中读取任务是不断循环的，每次栈被清空后，都会在消息队列中读取新的任务，如果没有新的任务，就会等待，直到有新的任务，这就叫事件循环。

#### 3.1 Ajax异步请求
上图以AJAX异步请求为例，发起异步任务后，由AJAX线程执行耗时的异步操作，而JS引擎线程继续执行堆中的其他同步任务，直到堆中的所有异步任务执行完毕。然后，从消息队列中依次按照顺序取出消息作为一个同步任务在JS引擎线程中执行，那么AJAX的回调函数就会在某一时刻被调用执行。

#### 3.2 示例
> <br>执行下面这段代码，执行后，在 5s 内点击两下，过一段时间（>5s）后，再点击两下，整个过程的输出结果是什么？
<br>

```JavaScript
setTimeout(function(){
    for(var i = 0; i < 100000000; i++){}
    console.log('timer a');
}, 0)

for(var j = 0; j < 5; j++){
    console.log(j);
}

setTimeout(function(){
    console.log('timer b');
}, 0)

function waitFiveSeconds(){
    var now = (new Date()).getTime();
    while(((new Date()).getTime() - now) < 5000){}
    console.log('finished waiting');
}

document.addEventListener('click', function(){
    console.log('click');
})

console.log('click begin');
waitFiveSeconds();
```
```JavaScript
setTimeout
```
作用是在间隔一定的时间后，将回调函数插入消息队列中，等栈中的同步任务都执行完毕后，再执行。因为栈中的同步任务也会耗时，所以间隔的时间一般会大于等于指定的时间。

```JavaScript
setTimeout(fn, 0)
```
作用是将回调函数fn立刻插入消息队列，等待执行，而不是立即执行。看一个例子：

```JavaScript
setTimeout(function() {
    console.log("a")
}, 0)

for(let i=0; i<10000; i++) {}
console.log("b")
```


```JavaScript
b a
```
打印结果表明回调函数并没有立刻执行，而是等待栈中的任务执行完毕后才执行的。栈中的任务执行多久，它就得等多久。

所以，输出结果就容易得出了。
首先，先执行同步任务。其中waitFiveSeconds是耗时操作，持续执行长达5s。

```JavaScript
0
1
2
3
4
click begin
finished waiting
```

然后，在JS引擎线程执行的时候，'timer a'对应的定时器产生的回调、 'timer b'对应的定时器产生的回调和两次 click 对应的回调被先后放入消息队列。由于JS引擎线程空闲后，会先查看是否有事件可执行，接着再处理其他异步任务。因此会产生下面的输出顺序。


```JavaScript
click
click
timer a
timer b
```

最后，5s 后的两次 click 事件被放入消息队列，由于此时JS引擎线程空闲，便被立即执行了。

```JavaScript
click
click
```


<br>





