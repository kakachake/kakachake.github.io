---
title: js知识点
date: 2022-04-12 09:46:45
tags: ["javascript"]
categories: ["javascript"]
---

## JavaScript 中 ?? 与 || 的区别

用法相同，都是前后是值，中间用符号连接。根据前面的值来判断最终返回前面的值还是后面的值。

值 1 ?? 值 2

值 1 || 值 2

## 不同点

判断方式不同：

使用 ?? 时，只有当值 1 为 null 或 undefined 时才返回值 2；

使用 || 时，值 1 会转换为布尔值判断，为 true 返回值 1，false 返回值 2

```javascript
// ??
undefined ?? 2; // 2
null ?? 2; // 2
0 ?? 2; // 0
"" ?? 2; // ""
true ?? 2; // true
false ?? 2; // false

// ||
undefined || 2; // 2
null || 2; // 2
0 || 2; // 2
"" || 2; // 2
true || 2; // true
false || 2; // 2
```
