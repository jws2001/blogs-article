# vue3中优雅的实现防抖

> 一般正确的写法都是调用防抖函数，返回一个新的函数来进行防抖。

就会出现下面的写法

```html
<template>
  <input type="text" :value="value" @input="handleInput" class="input">
  <p>{{ value }}</p>
</template>

<script setup lang="ts">
import { ref } from 'vue';
// 防抖函数
function doubonce(fn: Function, delay = 100) {
  let timer;
  return function (...arg) {
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(this, arg)
    }, delay)
  }
}

const value = ref('');
const handleInput = doubonce((e) => {
  value.value = e.target.value;
}, 1000)
</script>
```

这种写法是很常见的，能实现功能但是使用起来特别麻烦，需要手动将 v-model 拆开为 :value=xx @input=yy 在使用中非常不爽

# 巧妙利用vue3中的自定义 Ref， 来实现一个 doubonceRef

> doubonceRef 代码
```js
import { customRef } from 'vue';
export default function debounceRef(value, delay = 100) {
    let timer;
    return customRef((track, trigger) => {
        return{
            get(){
                // 手动调用收集依赖
                track();
                return value;
            },
            set(newValue){
                clearTimeout(timer);
                timer = setTimeout(() => {
                    value = newValue;
                    // 手动触发更新
                    trigger();
                }, delay)
            }    
        }
    })
}
```
使用 customRef 传递一个函数， 我们可以自己重新定义 set 函数

> 使用 doubonceRef

```html
<template>
  <input type="text" v-model="value" class="input">
  <p>{{ value }}</p>
</template>

<script setup lang="ts">
import debounceRef from './utils/debounceRef'
const value = debounceRef('', 1000);
</script>
```
在使用时就非常方便 只需要调用 debounceRef 传递初始值和延迟时间 就能完成防抖
