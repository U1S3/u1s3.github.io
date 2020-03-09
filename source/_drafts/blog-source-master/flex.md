---
title: Flex
comments: false
date: 2018-07-24 15:31:36
keywords: flex
description: Flex布局学习
summary: 今天不学明天不学后天会学？后天学个鸡儿。
categories: css
tags: [flex]
---

## 语法

### 基本

```html
<style>
    .box {
        display: flex;
        /* 或 display: inline-flex; */
    }
</style>
<!-- .box 是 flex容器 -->
<div class="box">
    <!-- .item 是 flex项目-->
    <span class="item"></span>
</div>
```

### 轴

flex容器默认存在两根轴，水平的主轴『main axis』和垂直的交叉轴『cross axis』。

- 主轴起始位置『main start』，结束位置『main end』；
- 交叉轴起始位置『cross start』，结束位置『cross end』；
- 项目默认沿着主轴排列，单个项目占据的主轴空间称为『main size』，占据的交叉轴空间称『cross size』。

### 容器属性

- `flex-direction` 决定主轴的方向，即项目的排列方向；

  ```css
  .box {
      flex-direction: row | row-reverse | column | column-reverse;
  }
  /*
    row：默认，主轴水平方向，左起；
    row-reverse：主轴水平方向，右起；
    column：主轴垂直方向，上起；
    column-reverse：主轴垂直方向，下起。
  */
  ```

- `flex-wrap` 换行；

  ```css
  .box {
      flex-wrap: nowrap | wrap | wrap-reverse;
  }
  /*
    nowrap：默认，不换行；
    wrap：换行，第一行在上方；
    wrap-reverse：换行，第一行在下方。
  */
  ```

- `flex-flow` 是 `flex-direction` 和 `flex-wrap` 的简写，默认值 `row nowrap`；

  ```css
  .box {
      flex-flow: <flex-direction> || <flex-wrap>;
  }
  ```

- `justify-content` 项目在主轴上的对齐方式；

  ```css
  .box {
      justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
  }
  /*
    flex-start：默认，左对齐；
    flex-end：右对齐；
    center：居中；
    space-between：两端对齐，项目之间间隔相等；
    space-around：项目间隔相等，项目与项目之间的间隔比项目与边框的间隔大一倍；
    space-evenly：项目与项目，项目与边框的间隔都相等。
  */
  ```

- `align-items` 项目在交叉轴上的对齐方式；

  ```css
  .box {
      align-items: flex-start | flex-end | center | baseline | stretch;
  }
  /*
    flex-start：交叉轴起点对齐；
    flex-end：交叉轴终点对齐；
    center：交叉轴中点对齐，垂直居中；
    baseline：项目的第一行文字的基线对齐；
    stretch：默认，填满容器；
  */
  ```

- `align-content` 交叉轴方向上多轴线对齐方式；

  ```css
  .box {
      align-content: flex-start | flex-end | center | space-between | space-around | stretch;
  }
  /*
    flex-start：交叉轴起点对齐；
    flex-end：交叉轴终点对齐；
    center：交叉轴中点对齐；
    space-between：交叉轴两端对齐，项目之间间隔相等；
    space-around：项目间隔相等，项目与项目之间的间隔比项目与边框的间隔大一倍；
    stretch：默认，填满整个交叉轴；
  */
  ```

### 项目属性

- `order` 排序，asc排列，默认为 `0`，可为负值；

  ```css
  .item {
      order: <number>;
  }
  ```

- `flex-grow` 放大比例，默认为 `0`，即就算存在剩余空间也不放大，如果所有项目都为1则等分剩余空间，如果有一个为2其他为1则其占据的剩余空间比其他的项目多一倍；

  ```css
  .item {
      flex-grow: <number>;
  }
  ```

- `flex-shrink` 缩小比例，默认为 `1`，不能为负值，即如果空间不足，该项目将缩小，如果所有项目都为1，则当空间不足时，都将等比例缩小，如果有一个为0其他为1则当空间不足时其不缩小；

  ```css
  .item {
      flex-shrink: <number>;
  }
  ```

- `flex-basis` 定义项目在分配多余空间之前占据的主轴空间，浏览器根据此属性计算主轴是否有多余空间，默认为 `auto`，即项目本来的大小，可设置固定值，设置后项目占据固定空间；

  ```css
  .item {
      flex-basis: <length> | auto;
  }
  ```

- `flex` 是 `flex-grow` 、`flex-shrink` 和 `flex-basis` 的简写，默认值为 `0 1 auto`，后两属性为可选属性；

  ```css
  .item {
      flex: none | [ <flex-grow> <flex-shrink>? || <flex-basis> ]
  }
  /*
    此属性有连个快捷值，建议优先使用快捷值：
    auto = 1 1 auto；
    none = 0 0 auto。
  */
  ```

- `align-self` 允许单个项目有与其他项目不一样的对齐方式，会覆盖 `align-items` 属性。默认值为 `auto`，表示继承父元素的 `align-items` 属性，如果父元素没有设置则等同于 `stretch`；

  ```css
  .item {
      align-self: auto | flex-start | flex-end | center | baseline | stretch;
  }
  /*
    除了auto，其他属性与align-items完全一致。 
  */
  ```

## Links

- [css-tricks.com](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

- [实例演示](https://xluos.github.io/demo/flexbox/)

