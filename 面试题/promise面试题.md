# promise

> 这是一道最近很火的面试题，我试着分析它的执行过程并得到答案。


看下面的代码

```js
Promise.resolve()
    .then(() => {
        console.log(0);
        return Promise.resolve(4)
    }).then(res => {
        console.log(res)
    })


Promise.resolve()
    .then(() => {
        console.log(1)
    })
    .then(() => {
        console.log(2)
    })
    .then(() => {
        console.log(3)
    })
    .then(() => {
        console.log(5)
    })
    .then(() => {
        console.log(6)
    })

```

问代码打印的结果是什么?

## 开始分析

首先执行同步代码

第一个 ```Promise.resolve()``` 由于这个promise的状态是 **fulfilled** 所以会把后面的 **then** 的函数执行加入 **微队列**

```js
微队列：
    () => {
        console.log(0);
        return Promise.resolve(4)
    }
```

然后同步代码继续执行到第二个 ```Promise.resolve()``` 状态也是 **fulfilled** 也把后面的 **then** 的函数加入 **微队列**


```js
微队列：    
    () => {
        console.log(0);
        return Promise.resolve(4)
    }

    () => {
        console.log(1)
    }
```

到这里同步代码执行完毕，开始执行 **微队列** 里面的代码

执行

```js
微队列：
    () => {
        console.log(0);
        return Promise.resolve(4)
    }
```

控制台输出 **0** 然后这里返回的 ```Promise.resolve(4)``` 就是这道题的难点，解决了这个难点其他就不是问题，根据 [Promises/A+](https://duckduckgo.com) 规范规定 *If x is a promise, adopt its state* 意识就是，**then** 里面的函数执行返回的是一个**Promise** 那么当前的 **then** 的状态和返回的 **Promise状态保持一致**。

###至于怎么保持一致？

经过询问大佬：在 **V8** 引擎中对这个这块的处理是
**伪代码** ```Promise.resolve(4).then(() => { 在这里处理上面的 then })```  **V8** 会把这段代码加入微队列

```js
微队列：
    () => {
        console.log(1)
    }
    Promise.resolve(4).then(() => { 在这里处理上面的 then })
```

然后输出 **1** 然后把 ```() => {console.log(2)}``` 放入微队列

```js
微队列：
    Promise.resolve(4).then(() => { 在这里处理上面的 then })
    () => {
        console.log(2)
    }
```

然后执行 ```Promise.resolve(4).then(() => { 在这里处理上面的 then })``` 然后将 ```(4) => { 在这里处理上面的 then }``` 加入微队列

```js
微队列:
    () => {
        console.log(2)
    }
    (4) => { 在这里处理上面的 then }
```
然后输出 **2** 然后将 ```() => {console.log(3)}``` 加入微队列

```js
微队列:
    (4) => { 在这里处理上面的 then }
    () => {console.log(3)}
```

然后执行 ```(4) => { 在这里处理上面的 then }``` 在这里终于把 
```js
微队列： 
    then(() => {
        console.log(0);
        return Promise.resolve(4)
    })
``` 
这个 ***Promise** 状态改为 **fulfilled** 然后将 ```res => {console.log(res)}``` 加入微队列

```js
微队列：
    () => {console.log(3)}
    res => {console.log(res)}
```

输出 **3** 然后将  ```() => {console.log(5)}``` 加入微队列 

```js
微队列：
    res => {console.log(res)}
    () => {console.log(5)}
```
输出 res 也就是 **4** 
在输出 **5** 将 ```() => {console.log(6)}``` 加入微队列
输出 **6**

到此整个代码执行完毕。