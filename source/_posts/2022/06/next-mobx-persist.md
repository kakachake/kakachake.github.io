---
title: 在next中使用mobx并做持久化
date: 2022-06-25 20:12:16
tags: [react, next, mobx, persist]
categories: [react, next]
---

最近使用 next 构建一个项目，使用 mobx 来管理数据，并且做持久化。这里记录下遇到的问题

持久化使用的插件为[mobx-persist-store](https://github.com/quarrant/mobx-persist-store)
首先是 storage 配置项，由于 nextjs 首先进行服务端渲染，所以并没有 window 对象，故配置的 localStorage 获取不到，当然这里在服务端页不需要获取，故我们可以通过**typeof 来判断 window 是否存在**，如果存在，则进行持久化操作，否则不进行持久化操作。
核心代码为：

```javascript
typeof window !== "undefined" &&
  makePersistable(yourstore, {
    name: "yourStoreName",
    properties: ["yourProperties"],
    storage: localStorage, //or other storage
  });
```

看起来不错，代码将会只在客户端运行，并作持久化操作，但是运行起来却发现，next 报错了：

```javascript
Hydration failed because the initial UI does not match what was rendered on the server
```

原因在这[react-hydration-error](https://nextjs.org/docs/messages/react-hydration-error)

简单说就是服务端渲染的 dom 树与客户端首次渲染不同，Hydration 报错了

解决方案也很简单，就是保证服务端和客户端渲染相同，那么就要分析一下为什么服务端和客户端渲染不同呢，这里想到前面写的`typeof window !== "undefined"`，这会导致只有客户端会进行持久化，拿到 localStorage 并渲染，所以服务端渲染的 dom 树与客户端渲染的 dom 树不同，这样就会报错了。

解决方法就是将 store 的内容延迟渲染到页面上，即使用 componentDidMount 或 useEffect，在页面渲染完成后，将 store 的内容渲染到页面上，这样就不会报错了。

核心代码：

```javascript
export const StoreProvider = ({ initialValue, children }: IProps) => {
  // 创建一个store
  const [store, setStore] = useState(initialValue);

  // 创建响应式数据
  const obStore: IStore = useLocalObservable(cstore);

  //等到客户端渲染阶段再赋值
  useEffect(() => {
    setStore(obStore);
  }, [obStore]);
  // 将响应式数据作为provider的value传入上下文
  return (
    <StoreContext.Provider value={store}>{children}</StoreContext.Provider>
  );
};
```
