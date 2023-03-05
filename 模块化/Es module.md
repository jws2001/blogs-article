# Es module

关键词：

- 官方标准
- 使用新语法实现
- 所有环境均支持
- 同时支持静态依赖和动态依赖 (静态依赖：在代码运行前就要确定依赖关系)
- 动态依赖是异步的
- 符号绑定

## 符号绑定

> 符号绑定指的是 在使用 ``` import a from './a.js' ``` 这里的 **a** 并不是一个简单的赋值语句，看下面例子

```js

// module a.js
export let a = 1;

export function setA () {
    a = 2;
}


// module b.js
import { a, setA } from './a.js'
console.log(a); // 1
setA();
console.log(b); // 2

```

从上面的例子中 第一次 ``` a === 1 ``` 当调用 ``` setA ``` 之后 ``` a === 2 ```

这里的 ``` a ``` 使用的是符号绑定（相当于绑定的是同一块内存空间） 并不是赋值语句需要注意。

