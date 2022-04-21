---
title: vuex持久化简单实现
date: 2022-04-21 17:16:03
tags: [vuex,vue,vuex持久化]
categories: [vuex, vue]
---
## 需求

在使用vuex时，经常会遇到需要数据持久化的需求，比如用户的token、用户的个人信息等，这些数据在登录成功后，需要持久化到本地，以便下次登录时使用。

## 实现

### vuex Plugin

这里使用[vuex的插件](https://vuex.vuejs.org/zh/guide/plugins.html)作为实现, 实现的内容有：

1. 当store初始化时，获取本地存储的数据，并设置到store中
2. 每次store提交mutation时，将store中指定的数据持久化到本地

### 用到的第三方库

1. [shvl](https://github.com/robinvdvleuten/shvl)：获取和设置对象内的点标记属性
2. [deepmerge](https://github.com/TehShrike/deepmerge)：深度合并两个或多个对象的可枚举属性。

### 具体实现如下：
  
  ```typescript
  import { Store } from 'vuex'
  import * as shvl from 'shvl'
  import merge from 'deepmerge'

  //根据该库编写
  //https://github.com/robinvdvleuten/vuex-persistedstate/blob/master/src/index.ts

  //接口
  interface Storage {
    getItem: (key: string) => any
    setItem: (key: string, value: any) => void
    removeItem: (key: string) => void
  }

  interface Options {
    key?: string
    paths: string[]
    storage: Storage
    arrayMerger?: (state: any[], saved: any[]) => any
  }

  //创建一个插件，传入所需的参数
  const createPersistedState = <State = any>(options: Options) => {
    const key = 'vuex'
    options = options ?? {}

    //从storage中获取数据
    function getState(key: string, storage: Storage) {
      const value = storage.getItem(key) ?? {}

      try {
        return typeof value === 'string'
          ? JSON.parse(value)
          : typeof value === 'object'
          ? value
          : {}
      } catch (err) {
        console.error(err)
      }

      return {}
    }

    //将stroage中的数据合并到state中
    function patchState(store: Store<State>, storage: Storage) {
      const storeState = getState(key, storage)
      console.log(storeState)

      store.replaceState(
        merge(store.state, storeState, {
          arrayMerge:
            options.arrayMerger ||
            function (store, saved) {
              return saved
            },
          clone: false
        })
      )
    }

    //将state中的数据保存到stroage中
    function setState(key: string, state: any, storage: Storage) {
      return storage.setItem(key, JSON.stringify(state))
    }

    /**
     * 给定一个路径数组及状态，返回一个对象并按照路径设置值
     * @param state：状态
     * @param paths：路径数组
     * @returns
     */
    function reducer(state: any, paths: string[]) {
      return Array.isArray(paths)
        ? paths.reduce(function (substate, path) {
            return shvl.set(substate, path, shvl.get(state, path))
          }, {})
        : state
    }

    //返回plugin函数
    return (store: Store<any>) => {
      //首先将stroage中的数据合并到state中
      patchState(store, options.storage)

      //然后监听state的变化，并将需要保存的数据保存到stroage中
      store.subscribe((mutation, state) => {
        setState(key, reducer(state, options.paths), options.storage)
      })
    }
  }

  export default createPersistedState
  ```
  
## 使用

导入插件，并在vuex的plugins中注册，将需要持久化的数据路径指定到paths中，并将stroage指定为本地存储，如下：

```typescript
import { createStore } from 'vuex'
import createPersistedState from 'persistedstate'
export default createStore<any>({
  state: () => {
    return {
      name: 'store',
    }
  },
  getters: {},
  mutations: {},
  actions: {},
  modules: { loginModule },
  plugins: [
    createPersistedState({
      paths: ['loginModule.userInfo', 'loginModule.token'],
      storage: window.localStorage
    })
  ]
})
```
