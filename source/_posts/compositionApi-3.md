---
title: compositionApi-3
date: 2022-04-01 08:35:41
tags: [vue3, compositionApi, 生命周期]
categories: vue3
---

## 生命周期钩子

setup 可以用来替代 data、methods、computed 、watch 等等这些选项，也可以替代生命周期钩子。

| 选项式 API        | Hook inside `setup` |
| ----------------- | ------------------- |
| `beforeCreate`    | Not needed\*        |
| `created`         | Not needed\*        |
| `beforeMount`     | `onBeforeMount`     |
| `mounted`         | `onMounted`         |
| `beforeUpdate`    | `onBeforeUpdate`    |
| `updated`         | `onUpdated`         |
| `beforeUnmount`   | `onBeforeUnmount`   |
| `unmounted`       | `onUnmounted`       |
| `errorCaptured`   | `onErrorCaptured`   |
| `renderTracked`   | `onRenderTracked`   |
| `renderTriggered` | `onRenderTriggered` |
| `activated`       | `onActivated`       |
| `deactivated`     | `onDeactivated`     |

可以看到 beforeCreate 和 create 在 setupApi 中是没有的，我们可以直接在 setup 中编写原本需要在 create 中调用的函数。

因为 `setup` 是围绕 `beforeCreate` 和 `created` 生命周期钩子运行的，所以不需要显式地定义它们。换句话说，在这些钩子中编写的任何代码都应该直接在 `setup` 函数中编写。

```javascript
export default {
  setup() {
    const counter = ref(0);
    const increment = () => counter.value++;

    onMounted(() => {
      console.log("App Mounted1");
    });
    onMounted(() => {
      console.log("App Mounted2");
    });
    onUpdated(() => {
      console.log("App onUpdated");
    });
    onUnmounted(() => {
      console.log("App onUnmounted");
    });

    return {
      counter,
      increment,
    };
  },
};
```
