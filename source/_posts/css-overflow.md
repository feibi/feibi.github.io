---
title: overflow:auto 在chorme不同版本下表现差异
date: 2019-01-16
tags:
  - css
categories:
  - css
---

我们知道，设置`overflow: auto`后盒子会随着内容超出自动出现滚动条，而设置`overflow:scroll`滚动条会一直存在。但`overflow: auto`表现却是有差异的

> https://developer.mozilla.org/zh-CN/docs/Web/CSS/overflow

以 chrome49 与 chrome79 为例:

```html
<!-- html -->
<div class="box">
  <ul>
    <li>测试长度</li>
    ...
  </ul>
</div>
```

```css
/* css */
ul,
li {
  list-style: none;
  margin: 0;
  padding: 0;
}

.box {
  display: inline-block;
}

ul {
  box-shadow: 0 0 1px 1px #ddd;
  max-height: 200px;
  overflow: auto;
}

li {
  line-height: 30px;
  /* 撑开容器以完整显示 */
  white-space: nowrap;
}
```

- `overflow:auto` 表现不一致。可以看出 chorme79 下`.box的width`与 chorme49 下`content+滚动条width（通常默认是17px）`是相等的。说明在 chrome49 下`overflow:auto`滚动条占据了内容的部分宽度导致内容显示不全，而高版本 chrome79 下不占用内容宽度。

```css
ul {
  max-height: 200px;
  overflow: auto;
}
```

![overflow:auto](/lib/images/overflow.png)

- `overflow-y: scroll`表现一致，与 chrome79 下的`overflow: auto`效果相同

```css
ul {
  max-height: 200px;
  overflow-y: scroll;
}
```

![overflow:scroll](/lib/images/overflow_scroll.png)

为了抹平 chrome 之间差异，可用下面css方案解决，js方案不再赘述

- 定宽

```css
ul {
  max-height: 200px;
  overflow: auto;
  width: 81px;
}
```

- 自适应宽度

```css
ul {
  max-height: 200px;
  overflow-y: overlay;
  /* chrome 默认滚动条宽度是 17px */
  padding-right: 17px;
}
```

> overlay 行为与 auto 相同，但滚动条绘制在内容之上而不是占用空间。 仅在基于 WebKit（例如，Safari）和基于 Blink 的（例如，Chrome 或 Opera）浏览器中受支持。
