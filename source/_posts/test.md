---
title: test
date: 2022-03-19 15:59:40
tags: test
---

# 标题

## 二级标题

### 三级标题

```javascript
// import "css-loader!../css/style.css";
import "../css/style.css";
import "../css/image.css";
import "../css/title.less";
import img from "../img/nhlt.jpg";

const divEl = document.createElement("div");
divEl.className = "title";
divEl.innerHTML = "Hello World";

const bgDiv = document.createElement("div");
bgDiv.className = "image-bg";

const imgEl = document.createElement("img");
imgEl.className = "image-bg";
imgEl.src = img;

document.body.appendChild(divEl);
document.body.appendChild(bgDiv);
document.body.appendChild(imgEl);
```
