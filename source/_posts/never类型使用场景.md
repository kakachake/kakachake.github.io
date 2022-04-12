---
title: never类型使用场景
date: 2022-04-11 21:13:43
tags: ["typescript"]
categories: ["typescript"]
---

在之前学习 typescript 的时候看过 never 这个类型，并没有具体了解其使用场景，今天深入了解了下，发现官网给的例子非常好，这里来具体讲一下

- 首先定义这么一个函数：

  ```javascript
  function handleMessage(message: string | number) {
    switch (typeof message) {
      case "string":
        console.log("string 处理方式处理 message");
        break;
      case "number":
        console.log("number 处理方式处理 message");
        break;
    }
  }
  ```

- 可以看到该函数接收 message，其类型为 string 或者 number
- 假设未来某一天有人把 message 类型增加了 boolean,由于 switch 语句中没有对其做判断，所以传入该类型该函数是不会有任何效果的。所以我们针对这类情况需要一个提示。
- 这里我们可以使用 never 来解决这个问题：

  ```javascript
  function handleMessage(message: string | number) {
    switch (typeof message) {
      case "string":
        console.log("string处理方式处理message");
        break;
      case "number":
        console.log("number处理方式处理message");
        break;
      default:
        const check: never = message;
    }
  }
  ```

  可以看到我们在 switch 中增加了 default 分支，理论上 message 传入的是 string 或 number 类型，所以 defalt 分支永远都不会进入，但是如果有人做了如下这件事：

  ```javascript
  function handleMessage(message: string | number | boolean) {
    switch (typeof message) {
      case "string":
        console.log("string处理方式处理message");
        break;
      case "number":
        console.log("number处理方式处理message");
        break;
      default:
        const check: never = message;
    }
  }
  ```

  我们可以看到 message 新增了 boolean 类型，这时 switch 语句就可能会走到 default 分支，但是由于赋值语句 check 的变量类型为 never,由于**never 不可以被其他类型的变量赋值**，所以此条语句汇报错误：
  {% asset_img 20220411213103.png slug %}

- 以上就是 never 的一个使用场景
