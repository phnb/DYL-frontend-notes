# Some tricks of "React Hook"

[参考文章](http://www.ruanyifeng.com/blog/2020/09/react-hooks-useeffect-tutorial.html)

###1. useEffect 第一次不执行

运用useRef和useLayoutEffect来实现useEffect的功能，并且第一次渲染不执行。

``` JavaScript
const firstUpdate = useRef(true);
useLayoutEffect(() => {
    if (firstUpdate.current) {
        firstUpdate.current = false;
        return;
    }
    else {
        handleChange(tagValue);
    }
}, [tagValue]);
```

补充，useRef和useLayoutEffect的用法：
<br>

#### 1.1 UseRef

``` JavaScript
const refContainer = useRef(initialValue);
```

useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的整个生命周期内保持不变。

一个常见的用例便是命令式地访问子组件：

```JavaScript
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

需要注意的是，当 ref 对象内容发生变化时，useRef 并不会通知你。变更 .current 属性不会引发组件重新渲染。如果想要在 React 绑定或解绑 DOM 节点的 ref 时运行某些代码，则需要使用回调 ref 来实现。
<br>

#### 1.2 useLayoutEffect

其函数签名与 useEffect 相同，但它会在所有的 DOM 变更之后同步调用 effect。可以使用它来读取 DOM 布局并同步触发重渲染。在浏览器执行绘制之前，useLayoutEffect 内部的更新计划将被同步刷新。
<br>


###2. 同一个dev中实现单击和双击⌚️

通过setTimeout()设置一个延时200ms,并通过一个标志位变量count记录当前点击次数

``` JavaScript
let count = 0;
class App extends Component {
    onClick = () => {
        count += 1;
        setTimeout(() => {
          if (count === 1) {
            console.log('single click: ', count);
          } else if (count === 2) {
            console.log('setTimeout onDoubleClick: ', count);
          }
          count = 0;
        }, 300);
    }
    
    render(){
        return (...)
    }
}
```

再加个 else if (count === 3) 还能实现 3 击。