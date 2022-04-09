---
title: 在vue3中使用vuex——hooks调用
date: 2022-04-08 19:32:28
tags: ["vue3", "vuex", "hooks"]
---

在 vue3 中的 setup 中使用 vuex，需要使用 computed 进行响应式，如果变量过多，考虑使用 mapState 或者 mapGetters，然而 setup 中没有 this 属性，导致原 vue2 的 computed 扩展写法失效，这里提供一种思路，编写了一个 hooks，使得可以在 setup 中完美使用 vuex。

## useMapState

```javascript
import { mapState, useStore } from "vuex";
import { computed } from "vue";
export const useMapState = (map) => {
  //拿到store对象
  const store = useStore();

  //获取到mapState对应对象的function
  const storeState = mapState(map);

  //对数据进行转换，将每个函数传入到computed中
  const states = {};
  Object.keys(storeState).forEach((key) => {
    //通过bind绑定一个含有$store的this
    states[key] = computed(storeState[key].bind({ $store: store }));
  });
  return states;
};
```

## useMapGetters

```javascript
import { mapGetters, useStore } from "vuex";
import { computed } from "vue";
export const useMapGetters = (map) => {
  //拿到store对象
  const store = useStore();

  //获取到mapState对应对象的function
  const storeState = mapGetters(map);

  //对数据进行转换，将每个函数传入到computed中
  const getters = {};
  Object.keys(storeState).forEach((key) => {
    //通过bind绑定一个含有$store的this
    getters[key] = computed(storeState[key].bind({ $store: store }));
  });
  return getters;
};
```

## useMapper

我们也可以将上述的函数封装成一个统一的 hook,由用户自行传入 mapper 函数：

```javascript
import { useStore } from "vuex";
import { computed } from "vue";
/**
 *
 * @param {*} mapper
 * @param {*} mapperFn mapper函数：mapGetters|mapState
 * @returns
 */
export const useMapper = (mapper, mapperFn) => {
  //拿到store对象
  const store = useStore();

  //获取到mapState对应对象的function
  const storeState = mapperFn(mapper);

  //对数据进行转换，将每个函数传入到computed中
  const states = {};
  Object.keys(storeState).forEach((key) => {
    //通过bind绑定一个含有$store的this
    states[key] = computed(storeState[key].bind({ $store: store }));
  });
  return states;
};
```
