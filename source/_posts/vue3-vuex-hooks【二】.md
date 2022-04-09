---
title: vue3-vuex-hooks【二】
date: 2022-04-09 20:14:55
tags: ["vue3", "vuex", "hooks"]
---

书接上回，在实现 vuex hooks 的第一版中，未考虑到分模块的问题，这里更新下 useMapGetters 和 useMapState 两个函数

这里主要用到了 `createNamespacedHelpers` 这个函数，他可以通过使用 `createNamespacedHelpers` 创建基于某个命名空间辅助函数。
它返回一个对象，对象里有新的绑定在给定命名空间值上的组件绑定辅助函数

## useMapGetters

```javascript
import { createNamespacedHelpers, mapGetters, useStore } from "vuex";
import { computed } from "vue";
import { useMapper } from "./useMapper";
export const useMapGetters = (moduleName, mapper) => {
  let mapperFn = mapGetters;
  if (typeof moduleName === "string" && moduleName.length > 0) {
    //绑定namespace
    mapperFn = createNamespacedHelpers(moduleName).mapGetters;
  } else {
    mapper = moduleName;
  }
  return useMapper(mapper, mapperFn);
};
```

## useMapState

```javascript
import { createNamespacedHelpers, mapState, useStore } from "vuex";
import { computed } from "vue";
import { useMapper } from "./useMapper";
export const useMapState = (moduleName, mapper) => {
  let mapperFn = mapState;
  if (typeof moduleName === "string" && moduleName.length > 0) {
    mapperFn = createNamespacedHelpers(moduleName).mapState;
  } else {
    mapper = moduleName;
  }
  return useMapper(mapper, mapperFn);
};
```
