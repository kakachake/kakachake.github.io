---
title: h函数
date: 2022-04-01 15:31:20
tags: ["vue3", "render"]
---

## h 函数使用

接收三个参数

- tag 标签的名字|组件|函数式组件
  'div'
- props：

  与 attribute、prop 和事件相应的对象。
  我们会在模板中使用

- 子元素

  子 VNodes，使用'h()'构建
  或使用字符串获取"文本 Vnode"或者有插槽的对象

```javascript
[
  "Some text comes first",
  h("h1", "A headline"),
  h(MyComponent, {
    someprop: "foobar",
  }),
];
```

**下面是一个简单的例子**

```javascript
import { h } from "vue";
export default {
  render() {
    return h(
      "h2",
      {
        class: "title",
      },
      "Hello Render"
    );
  },
};
```
