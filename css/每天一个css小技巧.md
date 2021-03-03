#### 考虑屏幕 dpr -- 响应式图片

正常情况下，图片的展示应该没有什么问题了。但是对于有图片可展示的情况下，我们还可以做的更好。

在移动端或者一些高清的 PC 屏幕（苹果的 MAC Book），屏幕的 dpr 可能大于 1。这种时候，我们可能还需要考虑利用多倍图去适配不同 dpr 的屏幕。

正好，`<img>` 标签是有提供相应的属性 `srcset` 让我们进行操作的。

```HTML
<img src='photo@1x.png'
   srcset='photo@1x.png 1x,
           photo@2x.png 2x,
           photo@3x.png 3x' 
/>
```

当然，这是比较旧的写法，`srcset` 新增了新的 w 宽度描述符，需要配合 `sizes` 一起使用，所以更好的写法是：

```HTML
<img 
        src = "photo.png" 
        sizes = “(min-width: 600px) 600px, 300px" 
        srcset = “photo@1x.png 300w,
                       photo@2x.png 600w,
                       photo@3x.png 1200w,
>
```

利用 `srcset`，我们可以给不同 dpr 的屏幕，提供最适合的图片。

> 上述出现了一些概念，dpr，图片的 srcset ，sizes 属性，不太了解的可以移步 [前端基础知识概述](https://github.com/chokcoco/cnblogsArticle/issues/25)

#### 图片丢失

好了，当图片链接没问题时，已经处理好了。接下来还需要考虑，当图片链接挂了，应该如何处理。

处理的方式有很多种。最好的处理方式，是我最近在张鑫旭老师的这篇文章中 -- [图片加载失败后CSS样式处理最佳实践](https://www.zhangxinxu.com/wordpress/2020/10/css-style-image-load-fail/) 看到的。这里简单讲下：

1. 利用图片加载失败，触发 `<img>` 元素的 `onerror` 事件，给加载失败的 `<img>` 元素新增一个样式类
2. 利用新增的样式类，配合 `<img>` 元素的伪元素，展示默认兜底图的同时，还能一起展示 `<img>` 元素的 `alt` 信息

```HTML
<img src="test.png" alt="图片描述" onerror="this.classList.add('error');">

img.error {
    position: relative;
    display: inline-block;
}

img.error::before {
    content: "";
    /** 定位代码 **/
    background: url(error-default.png);
}

img.error::after {
    content: attr(alt);
    /** 定位代码 **/
}
```

我们利用伪元素 `before` ，加载默认错误兜底图，利用伪元素 `after`，展示图片的 `alt` 信息：

![image](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/db77417d46564081b2118bae78a09956~tplv-k3u1fbpfcp-zoom-1.image)

OK，到此，完整的对图片的处理就算完成了，完整的 Demo 你可以戳这里看看：

[CodePen Demo -- 图片处理](https://codepen.io/Chokcoco/pen/WNGgNqv?editors=1100)

#### 滚动平滑：使用 `scroll-behavior: smooth` 让滚动丝滑

使用 `scroll-behavior: smooth`，可以让滚动框实现平稳的滚动，而不是突兀的跳动。看看效果，假设如下结构：

```HTML
<div class="g-container">
  <nav>
    <a href="#1">1</a>
    <a href="#2">2</a>
    <a href="#3">3</a>
  </nav>
  <div class="scrolling-box">
    <section id="1">First section</section>
    <section id="2">Second section</section>
    <section id="3">Third section</section>
  </div>
</div>
复制代码
```

不使用 `scroll-behavior: smooth`，是突兀的跳动切换：

![scrol](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ece4fb09a046457f9b59ec8dc9e82f86~tplv-k3u1fbpfcp-zoom-1.image)

给可滚动容器添加 `scroll-behavior: smooth`，实现平滑滚动：

```CSS
{
    scroll-behavior: smooth;
}
复制代码
```

![scroll2](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/efcabe865fa44d07b89d48b6ba4d4c40~tplv-k3u1fbpfcp-zoom-1.image)

#### 使用 `scroll-snap-type` 优化滚动效果

`sroll-snap-type` 可能算得上是新的滚动规范里面最核心的一个属性样式。

**[scroll-snap-type](https://developer.mozilla.org/zh-CN/docs/Web/CSS/scroll-snap-type)**：属性定义在滚动容器中的一个临时点（snap point）如何被严格的执行。

光看定义有点难理解，简单而言，这个属性规定了一个容器是否对内部滚动动作进行捕捉，并且规定了如何去处理滚动结束状态。让滚动操作结束后，元素停止在适合的位置。

看个简单示例：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fdc2b27afaef4b5782842a74cbe7cdef~tplv-k3u1fbpfcp-zoom-1.image)

当然，`scroll-snap-type` 用法非常多，可控制优化的点很多，限于篇幅无法一一展开，具体更详细的用法可以看看我的另外一篇文章 -- [使用 sroll-snap-type 优化滚动](https://github.com/chokcoco/iCSS/issues/74)

#### 控制滚动层级，避免页面大量重排

这个优化可能稍微有一点难理解。需要了解 CSS 渲染优化的相关知识。

先说结论，控制滚动层级的意思是**尽量让需要进行 CSS 动画（可以是元素的动画，也可以是容器的滚动）的元素的 z-index 保持在页面最上方，避免浏览器创建不必要的图形层（GraphicsLayer），能够很好的提升渲染性能**。

这一点怎么理解呢，一个元素触发创建一个 Graphics Layer 层的其中一个因素是：

- 元素有一个 z-index 较低且包含一个复合层的兄弟元素

根据上述这点，我们对滚动性能进行优化的时候，需要注意两点：

1. 通过生成独立的 GraphicsLayer，利用 GPU 加速，提升滚动的性能
2. 如果本身滚动没有性能问题，不需要独立的 GraphicsLayer，也要注意滚动容器的层级，避免因为层级过高而被其他创建了 GraphicsLayer 的元素合并，被动的生成一个 Graphics Layer ，影响页面整体的渲染性能

如果你对这点还有点懵，可以看看这篇文章 -- [你所不知道的 CSS 动画技巧与细节](https://github.com/chokcoco/iCSS/issues/27)

### 焦点响应

`font-family: system-ui` 能够自动选择本操作系统下的默认系统字体。

`:focus-visible`：这个选择器可以有效地根据用户的输入方式(鼠标 vs 键盘)展示不同形式的焦点。

有了这个伪类，就可以做到，当用户使用鼠标操作可聚焦元素时，不展示 `:focus` 样式或者让其表现较弱，而当用户使用键盘操作焦点时，利用 `:focus-visible`，让可获焦元素获得一个较强的表现样式。

看个简单的 Demo：

```HTML
<button>Test 1</button>
复制代码
button:active {
  background: #eee;
}
button:focus {
  outline: 2px solid red;
}
复制代码
```

使用鼠标点击：

![a11y3](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ca5170bb70934d729533f64b5e5db13d~tplv-k3u1fbpfcp-zoom-1.image)

可以看到，使用鼠标点击的时候，触发了元素的 `:active` 伪类，也触发了 `:focus`伪类，不太美观。但是如果设置了 `outline: none` 又会使键盘用户的体验非常糟糕。尝试使用 `:focus-visible` 伪类改造一下：

```CSS
button:active {
  background: #eee;
}
button:focus {
  outline: 2px solid red;
}
button:focus:not(:focus-visible) {
  outline: none;
}
复制代码
```

看看效果，分别是在鼠标点击 Button 和使用键盘控制焦点点击 Button：

![a11y4](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e6a93f5cfc294a6d9316eda9c33762f7~tplv-k3u1fbpfcp-zoom-1.image)

[CodePen Demo -- :focus-visible example](https://codepen.io/Chokcoco/pen/abBbPrE)

可以看到，使用鼠标点击，不会触发 `:foucs`，只有当键盘操作聚焦元素，使用 Tab 切换焦点时，`outline: 2px solid red` 这段代码才会生效。

这样，我们就既保证了正常用户的点击体验，也保证了一批无法使用鼠标的用户的焦点管理体验。

值得注意的是，有同学会疑惑，这里为什么使用了 `:not` 这么绕的写法而不是直接这样写呢：

```CSS
button:focus {
  outline: unset;
}
button:focus-visible {
  outline: 2px solid red;
}
复制代码
```

为的是兼容不支持 `:focus-visible` 的浏览器，当 `:focus-visible` 不兼容时，还是需要有 `:focus` 伪类的存在。



作者：chokcoco
链接：https://juejin.cn/post/6932647134944886797
来源：掘金