# ES6中cont和let的暂时性死区
> js中的```typeof``` 后面写啥都不报错是真的么 ?

# cosnt,let的特点

在ES6中新增了 let 和 const 命令，用来申明变量。只是把传统的```var```进行了升级，但是使用 const，let 申明的变量只能在所在的代码块内有效。

```js
    {
        let a = 10;
        var b = 1;
        const c = 2;
    }
    console.log(b);//1
    console.log(a);//ReferenceError: a is not defined
    console.log(c);//ReferenceError: c is not defined
```
## 变量作用域
在上面的代码块中，分别使用了 ```let,const,var``` 生命了变量。然后在代码块之外访问者两个变量发现只有 通过```var```声明的变量可以被访问到。其它的就会报错。

## 不存在变量提升

通过```var```声明的变量，会存在变量提升的过程，即变量可以在声明之前使用，值为```undefined```。这种现象多多少少是有些奇怪的，按照一般的逻辑，变量应该在声明语句之后才可以使用。

## 暂时性死区
只要块级作用域内存在let/const命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。

```js
    var a = 123;
    if(true){
        a = 456; //Cannot access 'a' before initialization
        let a;
    }
```
在上面的代码中 ```a=456``` 是会报错的。 虽然存在全局变量a，但是在块级作用域中```let a```的存在使得这个区域中的 ```a``` 都是 ```let a``` 用于 a 还没有被初始化 提前使用a变量就会导致报错

## 不允许重复声明

使用 ```let,const```命名的变量，在当前的作用域内不得再次使用相同的 变量名。

## cosnt 和 let 的区别

```cosnt```申明的变量之后不得对该变量进行重新赋值 而 ```let```可以
所有一般习惯使用 ```cosnt``` 申明原始值的变量信息 ```let```作用域可能会改变的变量信息

# 总结

> 通过了解```cosnt,let```的特点; 我们知道了 ```cosnt和let```的暂时性死区；所以在想一下 ```typeof```后面写啥会报错


答案并不是-请看下面的代码
```js
typeof a; //由于代码存在暂时性死区 所以这个会报 a is not defined
let a;
```