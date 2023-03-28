# Vue3的生命周期

### onMounted

> 在组件挂在完成之后执行。

### onUpdated

> 在组件因为响应式状态变更而更新其DOM数之后执行。

### onUnmounted

> 在组件实例在卸载之后调用。

### onBeforeMount

> 在组件被挂载之前执行。

### onBeforeUpdate

> 在组件即将因为响应式状态变更而更新其DOM数之前调用。

### onBeforeUnmount

> 在组件实例被卸载之前调用。

### onActivated

> 若组件实例是 ```<KeepAlive>``` 缓存树的一部分，当组件被插入到 DOM 中时调用。

### onDeactivated

> 若组件实例是 ```<KeepAlive>``` 缓存树的一部分，当组件从 DOM 中被移除时调用。

### onErrorCaptired

> 捕获了后代组件传递的错误时调用。

### onRenderTracked

这个钩子仅在开发模式下可用，且在服务器端渲染期间不会被调用。

> 当组件渲染过程中追踪到响应式依赖时调用。

### onRenderTriggered

这个钩子仅在开发模式下可用，且在服务器端渲染期间不会被调用。

> 当响应式依赖的变更触发了组件渲染时调用。

### onServerPrefetch （SSR only）

> 在组件实例在服务器上被渲染之前调用。
