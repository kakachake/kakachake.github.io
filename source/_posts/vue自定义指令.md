---
title: vue自定义指令
date: 2022-04-02 09:59:29
tags: ["自定义指令"]
---

## 官方文档

[自定义指令](https://v3.cn.vuejs.org/guide/custom-directive.html#%E7%AE%80%E4%BB%8B)

## 使用场景

通常情况下，你需要对 DOM 元素进行**底层操作**，这个时候就会用到**自定义指令**

## 一个简单的自定义局部指令 v-focus

```javascript
<template>
  <div>
    <input v-focus type="text" ref="input" />
  </div>
</template>

<script>
export default {
  directives: {
    focus: {
      created(){

      },
      mounted(el) {
        console.log(el);
        el.focus();
      },
    },
  },
};
</script>
```

## 自定义全局指令

```javascript
const app = createApp(App);
app.directive("focus", {
  mounted(el, bindings, vnode, preVnode) {
    console.log("focus mounted");
    el.focus();
  },
});

app.mount("#app");
```

## 指令的生命周期

- `created`：在绑定元素的 attribute 或事件监听器被应用之前调用。在指令需要附加在普通的 `v-on` 事件监听器调用前的事件监听器中时，这很有用。

- `beforeMount`：当指令第一次绑定到元素并且在挂载父组件之前调用。

- `mounted`：在绑定元素的父组件被挂载前调用。

- `beforeUpdate`：在更新包含组件的 VNode 之前调用。

- `updated`：在包含组件的 VNode **及其子组件的 VNode** 更新后调用。

- `beforeUnmount`：在卸载绑定元素的父组件之前调用

- `unmounted`：当指令与元素解除绑定且父组件已卸载时，只调用一次。
