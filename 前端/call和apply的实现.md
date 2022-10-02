# call和apply的模拟实现

> 在很多的 技术网址上都会看见 不同方式的实现，今天突发奇想 自己也来写一下

## call 模拟实现

```js
    const a = {name:'song'}
    function print(){
        console.log(this.name)
    }
    print.call(a);//song

    //假如
    a = {
        name:'song',
        print(){
            console.log(this.name)
        }
    }
    a.print();//song

    //这样子就改变了this
```

第一版(ES6写法)

```js
Function.prototype.myCall = function (context, ...arg) {
    //这里的 this 就是要改变的 function
    context.__myCall__ = this
    context.__myCall__(...arg)
    //在删除这个属性
    delete context.__myCall__
}
```

第一版(E5写法)

```js
Function.prototype.myCall = function (context) {
    //这里的 this 就是要改变的 function
    context.__myCall__ = this;
    //ES5只能采取 argument 获取实参了
    var arg = [];
    for (var i = 1, len = arguments.length; i < len; i++) {
        arg.push('arguments[' + i + ']')
    }
    //ES5中 我们可以借用 不常用的 eval 函数了
    eval('context.__myCall__(' + arg + ')')
    //在删除这个属性
    delete context.__myCall__
}


//测试一下
var person = {
    name: 'song'
}

function print(age,sex) {
    console.log(this.name,age)
}

print.myCall(person,18,'男'); //song 18 男
```

到这里函数完成了 80% 但是它并不完善、我还需要注意一下几点

1. this 参数可以传 null，当为 null 的时候，视为指向 window
2. 函数是可以有返回值的！

第二版(ES6写法)

```js

Function.prototype.myCall = function (context, ...arg) {
    //这里的 this 就是要改变的 function
    context = context || global || window //考虑了一下 node 环境
    context.__myCall__ = this
    //保存函数的执行结果
    const result = context.__myCall__(...arg)
    //在删除这个属性
    delete context.__myCall__
    return result
}

```

第二版(ES5写法)

```js

Function.prototype.myCall = function (context) {
    //这里的 this 就是要改变的 function
    context = context || global || window //考虑了一下 node 环境
    context.__myCall__ = this;
    //ES5只能采取 argument 获取实参了
    var arg = [];
    for (var i = 1, len = arguments.length; i < len; i++) {
        arg.push('arguments[' + i + ']')
    }
    //ES5中 我们可以借用 不常用的 eval 函数了
    var result = eval('context.__myCall__(' + arg + ')')
    //在删除这个属性
    delete context.__myCall__
    return result
}
```

到此，我完成了 call 的模拟实现

## apply 模拟实现

apply 和 call 作用一样 就是传递的参数不同 稍微改造一下 call 就完成了

E6写法

```js
Function.prototype.myApply = function (context, arg) {
    //这里的 this 就是要改变的 function
    context = context || global || window //考虑了一下 node 环境
    context.__myCall__ = this
    //保存函数的执行结果
    const result = context.__myCall__(...arg)
    //在删除这个属性
    delete context.__myCall__
    return result
}

```

E5写法

```js

Function.prototype.myApply = function (context, data) {
    //这里的 this 就是要改变的 function
    context = context || global || window //考虑了一下 node 环境
    context.__myCall__ = this
    var arg = [];
    for (var i = 0, len = data.length; i < len; i++) {
        arg.push('data[' + i + ']')
    }
    //ES5中 我们可以借用 不常用的 eval 函数了
    var result = eval('context.__myCall__(' + arg + ')')
    //在删除这个属性
    delete context.__myCall__
    return result
}
```

> 总结：对 **eval** 函数有了新的认识