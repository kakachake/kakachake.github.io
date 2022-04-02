---
title: vue自定义指令
date: 2022-04-02 09:59:29
tags: ["自定义指令"]
---

## 官方文档

[自定义指令](https://v3.cn.vuejs.org/guide/custom-directive.html#%E7%AE%80%E4%BB%8B)

指令钩子传递了这些参数：

### el

指令绑定到的元素。这可用于直接操作 DOM。

### binding

包含以下 property 的对象。

- `instance`：使用指令的组件实例。
- `value`：传递给指令的值。例如，在 `v-my-directive="1 + 1"` 中，该值为 `2`。
- `oldValue`：先前的值，仅在 `beforeUpdate` 和 `updated` 中可用。无论值是否有更改都可用。
- `arg`：传递给指令的参数(如果有的话)。例如在 `v-my-directive:foo` 中，arg 为 `"foo"`。
- `modifiers`：包含修饰符(如果有的话) 的对象。例如在 `v-my-directive.foo.bar` 中，修饰符对象为 `{foo: true，bar: true}`。
- `dir`：一个对象，在注册指令时作为参数传递。例如，在以下指令中

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

## 自定义指令练习

### 自定义时间戳显示需求

- 在开发中，大多数情况下从**服务器**获取到的都是**时间戳**;
- 我们需要将**时间戳转换成具体格式化的时间**来展示;
- 在 Vue2 中我们可以通过~~过滤器（vue3 已经废弃）~~来完成;
- 在 Vue3 中我们可以通过**计算属性(computed)**或者自定义一个方法(methods)来完成;
- 其实我们还可以通过一个**自定义的指令**来完成;

### .vue 代码

思路：这里构建一个 v-format-time 的指令，传入 dayjs 格式化样式，并在元素上设置 data-time 传入时间戳，最终渲染出想要的格式化后的时间

```javascript
<template>
  <div>
    <h1 v-format-time :data-time="timestamp"></h1>
    <h1 v-format-time="'YYYY/MM/DD hh:mm:ss'" :data-time="timestamp"></h1>
  </div>
</template>

<script>
import { ref } from "vue";
export default {
  setup() {
    const timestamp = ref(null);

    setInterval(() => {
      timestamp.value = (new Date().getTime() / 1000).toFixed(0);
    }, 1000);

    return {
      timestamp,
    };
  },
};
</script>
```

### 指令代码

```javascript
import dayjs from "dayjs";
export default function registerFormatTime(app) {
  app.directive("format-time", {
    created(el, bindings) {
      console.log(el, bindings);
    },
    mounted() {},
    updated(el, bindings, vnode, lastVnode) {
      console.dir(el);
      //获取用户传过来的format
      const format = bindings.value || "YYYY-MM-DD hh:mm:ss";
      //获取元素上date-time的时间戳
      const timestamp = el.dataset.time;

      const date = timestamp.length === 10 ? +timestamp * 1000 : +timestamp;
      el.textContent = dayjs(date).format(format);
    },
  });
}
```
