# computed&v-model

> 这是vue中非常经典的问题，每当你用vue去封装一个表单组件的时候你就会发现这个问题。

在开发中我会自己封装一些表单组件，我会写以下代码。

```js
// 子组件
<template>
    <input type="text">
</template>

<script setup>
const proprs = defineProps(['modelValue']);
console.log(proprs.modelValue)
</script>

<style lang="scss" scoped>

</style>
```

再父组件中使用自组件

```js
//父组件
<script setup>
import { ref } from 'vue';
import Input from './components/input.vue'
const from = ref({
  place:'请填写你需要填写的信息',
  value:''
})
</script>

<template>
 <Input v-model="from"/>
</template>

<style scoped>

</style>
```

在这里会涉及到在使用**Input**使用```v-model```会绑定父组件自己创建的数据。

目前一切看起来很正常，但是在自组件我会使用这种写法

```html
<input type="text" v-model="proprs.modelValue.value">
```

这样做我就打破了 vue 的单项数据流，项目离屎山就不远了。

为了解决这个问题，在很多的第三方库中都使用的是 **computed** 包一层。

```js
<template>
    <input type="text" v-model="cupModelValue" :placeholder="props.modelValue.place">
</template>

<script setup>
import { computed } from '@vue/reactivity';

const props = defineProps(['modelValue']);
const emit = defineEmits(['update:modelValue']);

const cupModelValue = computed({
    get() {
        return props.modelValue.value
    },
    set(val) {
        emit('update:modelValue', {
            ...props.modelValue,
            value: val
        })
    }
})
</script>
```

这样写就完美解决了单项数据流的问题，但是这样有一个缺点，那就是麻烦，我不可能对每一个属性都这样写一遍。

```js

<template>
    <input type="text" v-model="cupModelValue.val" :placeholder="props.modelValue.place">
</template>

<script setup>
import { computed } from '@vue/reactivity';

const props = defineProps(['modelValue']);
const emit = defineEmits(['update:modelValue']);

const cupModelValue = computed({
    get() {
        return new Proxy(props.modelValue, {
            set(obj, name, val) {
                emit('update:modelValue', {
                    ...obj,
                    [name]: val
                })
                return true;
            }
        })
    },
    set(val){
        console.log(val)
        emit('update:modelValue',val)
    }
})
</script>

<style lang="scss" scoped></style>
```

我可以在计算属性里面返回一个 proxy，用一种巧妙的方法解决这个问题。

我将这个方法提交出来

```js
function useVModel(props, propName, emit) {
    return computed({
        get() {
            return new Proxy(props[propName], {
                set(obj, name, val){
                    emit(`update:${propName}`, {
                        ...obj,
                        [name]:val
                    })
                    return true
                }
            })
        }
    })
}
```

这样就将在使用类是传递对象的数据时的一种完美的解决方案。