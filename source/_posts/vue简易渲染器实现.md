---
title: vue简易渲染器实现
date: 2022-04-02 18:51:39
tags: ["minivue", "vue3"]
---

html：

```javascript
  <body>
    <div id="app"></div>
    <script src="./render.js"></script>
    <script>
      //1. 通过h函数来创建一个Vnode
      const vnode = h(
        "div",
        { class: "why", onClick: "(e)=>{console.log(e)}" },
        [h("h2", null, "当前计数：100"), h("button", null, "+1")]
      ); // vdom
      // console.log(vnode);

      //2. 通过mount函数，将vnode挂载到div#app上
      mount(vnode, document.querySelector("#app"));
    </script>
  </body>
```

js：

```javascript
const h = (tag, props, children) => {
  // vnode -> javascript对象 -> {}
  return {
    tag,
    props,
    children,
  };
};

const mount = (vnode, container) => {
  //vnode -> element
  // 在vnode上添加一份el真实dom
  // 1.创建出真实元素，并在vnode上保留el
  const el = (vnode.el = document.createElement(vnode.tag));
  console.log(el);

  // 2. 处理props
  if (vnode.props) {
    for (const key in vnode.props) {
      const value = vnode.props[key];
      if (key.startsWith("on")) {
        el.addEventListener(key.slice(2).toLocaleLowerCase(), eval(value));
      } else {
        el.setAttribute(key, value);
      }
    }
  }

  //3. 处理children
  if (vnode.children) {
    if (typeof vnode.children === "string") {
      el.textContent = vnode.children;
    } else {
      vnode.children.forEach((item) => {
        mount(item, el);
      });
    }
  }

  container.appendChild(el);
};
```
