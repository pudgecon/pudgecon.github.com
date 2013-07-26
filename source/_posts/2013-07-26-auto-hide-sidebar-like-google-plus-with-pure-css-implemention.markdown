---
layout: post
title: "auto hide sidebar like google plus with pure css implemention"
date: 2013-07-26 20:18
comments: true
categories:
  - CSS
  - HTML
---
<link href="/stylesheets/google-plus-example.css" rel="stylesheet"></link>

## 纯CSS实现的类似[google+](https://plus.google.com/)的自动隐藏的侧边栏

### 先看结果：

将鼠标放在`HOME`按钮上，出现侧边栏。

<div class="example google-plus-example">
  <div class="nav">
    <div class="home">
      <span class="icon icon-home"></span>
      <span>Home</span>
      <span>></span>
    </div>
    <div class="sidebar">
      <ul>
        <li>
          <span class="icon icon-home"></span>
          <span>Home</span>
        </li>
        <li class="devider"></li>
        <li>
          <span class="icon icon-profile"></span>
          <span>Profile</span>
        </li>
        <li>
          <span class="icon icon-circles"></span>
          <span>Circles</span>
        </li>
        <li>
          <span class="icon icon-photos"></span>
          <span>Photos</span>
        </li>
        <li class="divider"></li>
      </ul>
    </div>
  </div>
</div>

<!-- more -->

之前我们 [Ember JS 论坛](http://discuss.emberjs.cn)的QQ群上有人问起这个问题，现在我将自己的一个解决方案记下来。

我觉得之前更多的处理方法是采用`javascript`的方式来处理鼠标悬浮事件，而貌似比较少采用纯`CSS`的方式来处理，因此我觉得有记下来的一点意义。

### 首先先整理一下思路：

  1. 以某种方式隐藏侧边栏（sidebar）;

  1. 处理按钮的`hover`样式，以某种方式使sidebar出现;

  1. 处理sidebar的`hover`样式，以免当鼠标移出按钮区域，但还在sidebar上时，sidebar隐藏了;

  1. 适当加入一点点渐近效果。

### 开始实践：

* 首先是外层的wrapper的样式：

```css
.google-plus-example {
  position: relative;
  overflow: hidden;
}
```

将他的`position`设置为`relative`，这样内部的元素就以它为基准了（?）。

并且，设置它的`overflow`为`hidden`，这样我们下一步的sidebar躲起来的时候就不会露出来了。

* sidebar 原始样式

```css
.sidebar {
  position: absolute;
  top: 0;
  left: -200px;
  bottom: 0;
}
```

把它的`left`设置为`-200px`，让它躲在我们看不见的地方。具体这里为什么是`left`而不是直接隐藏起来，我们后面讲。

* 然后是设置按钮的悬浮（hover）样式

```css
.home:hover + .sidebar {
  left: 0;
}
```

这个CSS的作用是，当.home有鼠标悬浮（`:hover`）时，设置.sidebar的样式。

这里的`.home`指的就是HOME按钮。

把sidebar的`left`设回`0`，这样sidebar就出现了。

* 接下来是设置sidebar的hover样式，使得当鼠标不在按钮上面，但在它自身上面时，不隐藏
```css
.sidebar:hover {
  left: 0;
}
```

与上面是一样的。

* 最后，可能给它加一点点效果。

给sidebar加`transition`样式，指定为`left`变化时有效果：

```css
.sidebar {
  -webkit-transition: left .1s .1s linear;
     -moz-transition: left .1s .1s linear;
       -o-transition: left .1s .1s linear;
          transition: left .1s .1s linear;
}
```

给sidebar出现时添加一个阴影效果：
```css
.sidebar:hover,
.home:hover + .sidebar {
  -webkit-box-shadow: 0 0 40px rgba(0,0,0,.4);
     -moz-box-shadow: 0 0 40px rgba(0,0,0,.4);
       -o-box-shadow: 0 0 40px rgba(0,0,0,.4);
          box-shadow: 0 0 40px rgba(0,0,0,.4);
}

```

整体思路就是这样。

### 历史遗留问题

最后再来说说上面遗留的问题，为什么使用`left`属性而不是使用`display`属性。

其实，使用`display`属性是没有问题，初始情况下，设置`sidebar`的`display`属性为`none;`，当`hover`的时候将其的`display`属性设置为`block;`，问题是使用这个好像`transition`对其没有效果，所以我的选择是：使用`left`。希望知道其中奥妙的童鞋给我一个答案吧。

### P.S.

贴上面示例的代码：

{% include_code google-plus-example.html %}

{% include_code google-plus-example.css %}
