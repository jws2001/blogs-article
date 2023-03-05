# CommonJs 原理

> 写出 require 伪代码就知道大概的原理

1. 社区标准
2. 使用函数实现
3. 仅支持 node 环境
4. 动态依赖(需要代码运行后才能确定依赖)
5. 动态依赖是同步执行的

```js

function require(path){
    if(该模块有缓存结果么){
        return 缓存结果
    }   
    function _run(exports, require, module, __filename, __dirname){
        // 模块的代码都将放在这里执行
    }

    const module = {
        exports:{}
    }
    
    _run.call(
        module.exports,
        module.exports,
        require,
        module,
        模块路径,
        模块所在目录
    )
    
    把 module.exports 加入缓存结果

    return module.exports
}
```

总结：所有的模块代码 都是在函数中执行并且是同步的，所以不会污染全局变量。