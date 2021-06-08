# Fetch API 参考文档

[API参考文档](https://www.ruanyifeng.com/blog/2020/12/fetch-tutorial.html)

fetch() 是 XMLHttpRequest 的升级版，用于在 JavaScript 脚本里面发出 HTTP 请求。

浏览器原生提供这个对象。本文详细介绍它的用法。

## 基本用法
fetch() 的功能与 XMLHttpRequest 基本相同，但有三个主要的差异。
（1）fetch() 使用 Promise，不使用回调函数，因此大大简化了写法，写起来更简洁。
（2）fetch() 采用模块化设计，API 分散在多个对象上（Response 对象、Request 对象、Headers 对象），更合理一些；相比之下，XMLHttpRequest 的 API 设计并不是很好，输入、输出、状态都在同一个接口管理，容易写出非常混乱的代码。
（3）fetch() 通过数据流（Stream 对象）处理数据，可以分块读取，有利于提高网站性能表现，减少内存占用，对于请求大文件或者网速慢的场景相当有用。XMLHTTPRequest 对象不支持数据流，所有的数据必须放在缓存里，不支持分块读取，必须等待全部拿到后，再一次性吐出来。
在用法上，fetch()接受一个 URL 字符串作为参数，默认向该网址发出 GET 请求，返回一个 Promise 对象。它的基本用法如下。

``` JavaScript
fetch(url)
  .then(...)
  .catch(...)
```

下面是一个例子

``` JavaScript
//获取数据
const getEventActivities = (id, setWarning) => {
    return fetch(`${API}/event?eid=${id}`)     //url
    .then(res=>{
        if (res.status === 200){
            return res.json()
        } else if (res.status === 429) {
            setWarning(true)
        } else {
            if( res.status !== 403){
                setLoading(true)
                message.error("网络链接出了点问题...请刷新重试")
            }
        }
    }).then(data=>{ if (data){
        return data.result
    }})
}
```

## Response 对象：处理HTTP属性
#### 2.1 Response 对象的基本属性
下面的例子从服务器中获取JSON数据

```JavaScript
//fetch() 接收到的response是一个 Stream 对象
fetch('https://api.github.com/users/ruanyf')  //url
  .then(response => response.json())     //异步操作，取出所有内容并转换成JSON对象
  .then(json => console.log(json))
  .catch(err => console.log('Request Failed', err)); 
```

Response 包含的数据通过 Stream 接口异步读取，但是它还包含一些同步属性，对应 HTTP 回应的标头信息（Headers），可以立即读取, 比如response.status (最常用) 和 response.statusText。
<br>

##### Response.status
Response.status属性返回一个数字，表示 HTTP 回应的状态码（例如200，表示成功请求）。
##### Response.statusText
Response.statusText属性返回一个字符串，表示 HTTP 回应的状态信息（例如请求成功以后，服务器返回"OK"）

下面的例子通过辨别不同的<font color="red">status</font>来返回不同的信息:

```JavaScript
const getHost = () =>{
    if (uid){
        fetch("/api/host?uid="+uid)
        .then(response=>{
            if (response.status==200){
                return response.json()
            }else if (response.status == 429) {
                setWarning(true)
            }else{
                console.log("host数据获取失败")
            }
        })
        .then(data=>{
            setHost(data)
            // console.log(data)
        })
    }
}
```
其它的的headers标头信息属性：
##### Response.ok
Response.ok属性返回一个布尔值，表示请求是否成功，true对应 HTTP 请求的状态码 200 到 299，false对应其他的状态码。

##### Response.url
Response.url属性返回请求的 URL。如果 URL 存在跳转，该属性返回的是最终 URL。

##### Response.type
Response.type属性返回请求的类型。可能的值如下：
......

##### Response.redirected
Response.redirected属性返回一个布尔值，表示请求是否发生过跳转。

#### 2.2 判断请求是否成功
fetch()发出请求以后，有一个很重要的注意点：只有网络错误，或者无法连接时，fetch()才会报错，其他情况都不会报错，而是认为请求成功。

这就是说，即使服务器返回的状态码是 4xx 或 5xx，fetch()也不会报错（即 Promise 不会变为 rejected状态）。

只有通过Response.status属性，得到 HTTP 回应的真实状态码，才能判断请求是否成功。在上面那个例子中, status = 200说明请求成功，status = 429说明返回的状态是rejected.

```JavaScript
    const getHost = () =>{
        if (uid){
            fetch("/api/host?uid="+uid)
            .then(response=>{
                if (response.status==200){
                    return response.json()
                }else if (response.status == 429) {
                    setWarning(true)
                }else{
                    console.log("host数据获取失败")
                }
            })
            .then(data=>{
                setHost(data)
            })
        }
    }
```

另一种方法是判断response.ok是否为true

``` JavaScript
fetch('/api/get_cos_credential')
.then(res => {
    if (res.ok) return res.json()
    else if (res.status == 429) {
        setWarning(true)
    }
})
```

#### 2.3 读取内容的方法
Response对象根据服务器返回的不同类型的数据，提供了不同的读取方法。

- response.text()：得到文本字符串。
- response.json()：得到 JSON 对象。
- response.blob()：得到二进制 Blob 对象。
- response.formData()：得到 FormData 表单对象。
- response.arrayBuffer()：得到二进制 ArrayBuffer 对象。


上面5个读取方法都是异步的，返回的都是 Promise 对象。必须等到异步操作结束，才能得到服务器返回的完整数据。

## fetch() 的第二个参数：定制HTTP请求
fetch()的第一个参数是 URL，还可以接受第二个参数，作为配置对象，定制发出的 HTTP 请求。

```JavaScript
fetch(url, optionObj)
```

HTTP 请求的方法、标头、数据体都在这个对象里面设置。下面是一些示例

#### 3.1 POST 请求

```JavaScript
 fetch(`${API}/event/`,{
    method:(edit&&edit!=="null")?'PATCH':'POST',
    headers:{
        "content-type":"application/json",
    },
    body:JSON.stringify(body)
}).then(res=>{
    if(res.status===200){
        if(window.confirm("活动发布成功，点击确认回到组织管理页")){
            window.location.href="/manage"
        }
    }else if (res.status == 429) {
        setWarning(true)
    }
})
```

上面示例中，配置对象用到了三个属性:
- method：HTTP 请求的方法，POST、DELETE、PUT都在这个属性设置。
- headers：一个对象，用来定制 HTTP 请求的标头。
- body：POST 请求的数据体。

#### 3.2 提交JSON数据
还是上面这个例子：

```JavaScript
let body = {
    title,
    event_id:(edit&&edit!=="null")?parseInt(edit):undefined,
    start:all_day_check?start.format("YYYY-MM-DD"):start.format("YYYY-MM-DDTHH:mm:ss"),
    end:all_day_check?end.format("YYYY-MM-DD"):end.format("YYYY-MM-DDTHH:mm:ss"),
    location,
}

 fetch(`${API}/event/`,{
    method:(edit&&edit!=="null")?'PATCH':'POST',
    headers:{
        "content-type":"application/json",
    },
    body:JSON.stringify(body)
}).then(res=>{
    if(res.status===200){
        if(window.confirm("活动发布成功，点击确认回到组织管理页")){
            window.location.href="/manage"
        }
    }else if (res.status == 429) {
        setWarning(true)
    }
})
```

上面示例中，标头Content-Type要设成'application/json;charset=utf-8' 或者 'application/json'。Content-Type的默认值是'text/plain;charset=UTF-8', 因为默认发送的是纯文本.

#### 3.3 提交表单
``` JavaScript
const form = document.querySelector('form');

const response = await fetch('/users', {
  method: 'POST',
  body: new FormData(form)
})
```

#### 3.4 上传文件


#### 3.5 直接上传二进制数据
