# 克隆

> 克隆在开发中最常见不过了，但是我公司的克隆函数实在糟糕透了。

同事所写的克隆函数是完全可以覆盖开发中 80% 的场景，但是考虑欠缺，所以我要写出我认为最满意的克隆

```js
/**
 * 克隆函数 分为很多的版本 可以根据不同的情况使用不同的版本
 */
(function (global) {
    /**
     * 返回类型函数
     * @param {*} data 
     * @returns string
     */
    function myTypeOf(data) {
        return Object.prototype.toString.call(data).slice(8, -1)
    }
    /**
     * 基础版本
     * 在实际的生产中 基础版本就可以 解决80%的场景
     * clone 常规的 array object 以及一些基本的数据类型
     * @param {*} target 
     */
    global.baseClone = function (target) {
        if (typeof target !== 'object' || target === null) {
            //基本的数据类型不做处理
            return target;
        }
        let cloneTarget;
        if (myTypeOf(target) === 'Array') {
            //array 类型
            cloneTarget = [];
            for (let i = 0, len = target.length; i < len; i++) {
                cloneTarget[i] = global.baseClone(target[i]);
            }
            return cloneTarget;
        }
        //object类型
        cloneTarget = {};
        for (const key in target) {
            if (target.hasOwnProperty(key)) {
                cloneTarget[key] = global.baseClone(target[key])
            }
        }
        return cloneTarget
    }



    /**
     * 循环函数
     * @param {Array} data 
     * @param {Function} callBack 
     */
    function forEach(data, callBack) {
        for (let i = 0, len = data.length; i < len; i++) {
            callBack(data[i], i)
        }
    }
    /**
    * 在上面的版本中 就能覆盖实际场景的 80%
    * 但是 不够全面 没有考虑到循环引用 
    * 而且有代码的冗余  都使用到了 循环 我们可以抽离出来
    * WeakMap 的使用是为了考虑到 垃圾回收
    */
    global.baseClone2 = function (target, map = new WeakMap()) {
        if (typeof target !== 'object' || target === null) {
            //基本的数据类型不做处理
            return target;
        }

        let isArray = myTypeOf(target) === 'Array' ? true : false;

        const cloneTarget = isArray ? [] : {};

        //处理循环引用
        if (map.has(target)) {
            return map.get(target)
        }
        map.set(target, cloneTarget)

        const keys = isArray ? undefined : Object.keys(target);

        forEach(keys || target, (value, key) => {
            if (keys) {
                key = value;
            }
            cloneTarget[key] = global.baseClone2(target[key], map)
        })
        return cloneTarget;
    }

    /**
     * 初始化类型 
     */
    function getInit(target) {
        const Ctor = target.constructor;
        if (Ctor) {
            return new Ctor();
        }
        return Object.create(null)
    }

    /**
     * 第二版已经 解决了循环应用的问题 但是还不够健壮
     * 没有考虑其他的数据类型如 Date map set 等等
     * 还有一些对象的继承而来 我们是不是要考虑到 原型链上的东西
     * 下面写终极版
     */
    global.deepClone = function (target, map = new WeakMap()) {
        if (typeof target !== 'object' || target === null) {
            //基本的数据类型不做处理  函数也不再考虑的范围里面
            return target;
        }
        const cloneTarget = getInit(target)

        //处理循环引用
        if (map.has(target)) {
            return map.get(target)
        }
        map.set(target, cloneTarget)
        //处理时间类型
        if (myTypeOf(target) === 'Date') {
            return new Date(target.toString())
        }
        //处理map
        if (myTypeOf(target) === 'Map') {
            target.forEach((value, key) => {
                cloneTarget.set(key, global.deepClone(value, map))
            })
            return cloneTarget
        }
        //处理set
        if (myTypeOf(target) === 'Set') {
            target.forEach(value => {
                cloneTarget.add(global.deepClone(value, map))
            })
            return cloneTarget
        }

        //处理array obj
        let isArray = myTypeOf(target) === 'Array' ? true : false;
        const keys = isArray ? undefined : Object.keys(target);
        forEach(keys || target, (value, key) => {
            if (keys) {
                key = value
            }
            cloneTarget[key] = global.deepClone(target[key], map)
        })
        return cloneTarget;

    }



})(window._clone || (window._clone = {}))

```

> 在开发中 可以根据情况选择适合的克隆函数 已提升性能