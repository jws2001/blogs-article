# CommonJs 原理

> 写出 require 伪代码就知道大概的原理

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