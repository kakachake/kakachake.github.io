---
title: vuex中使用typescript解决方案
date: 2022-04-23 10:31:43
tags: [vuex, typescript]
categories: [vuex]
---

vuex对于typescript的兼容性一直有问题，尤其是使用useStore时，如果使用typescript给store了类型，那么在使用useStore时，只会显示根store的state的属性，对于modules的state属性，无法显示出来，而且使用时还会报错，导致只能将state设置为any，也没有了typescript的提示。

这里给出一种解决方法，就是重写一下useStore方法，使用typescript的类型，这样就可以显示出来modules的state属性了，并且不会报错。

```ts
export interface IRootState {
  name: string
}

//将IRootWithModule继承IRootState，并且将modules设置为IRootWithModule的属性，这样无论是根store还是modules，都可以访问到state属性了
export interface IRootWithModule extends IRootState {
  loginModule: ILoginState
}

//重写useStore方法，将IRootWithModule设置为useStore的返回值
export function useStore(): Store<IRootWithModule> {
  return useVuexStore()
}
```
