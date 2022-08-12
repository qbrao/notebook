<!-- TOC -->

- [CSS 垂直居中](#css-垂直居中)
  - [绝对定位](#绝对定位)
  - [`translate()`](#translate)
  - [视口单位](#视口单位)
  - [Flexbox](#flexbox)
  - [参考资料](#参考资料)

<!-- /TOC -->

# CSS 垂直居中

在 CSS 中对元素进行水平居中是非常简单的：如果它是一个行内元素，就对它的父元素应用 `text-align: center`；如果它是一个块级元素，就对它自身应用 `margin: auto`。

> “44 年前我们就把人类送上月球了，但现在我们仍然无法在 CSS 中实现垂直居中。”
> 
> ——James Anderson（https://twitter.com/jsa/status/358603820516917249）

## 绝对定位

这个方法最大的局限在于它要求元素的宽高是固定的。

```css
main {
  position: absolute;
  top: 50%;
  left: 50%;
  margin-top: -3em; /* 6/2 = 3 */
  margin-left: -9em; /* 18/2 = 9 */
  width: 18em;
  height: 6em;
}
```

```css
main {
  position: absolute;
  top: calc(50% - 3em);
  left: calc(50% - 9em);
  width: 18em;
  height: 6em;
}
```

## `translate()`

问题：

- 我们有时不能选用绝对定位，因为它对整个布局的影响太过强烈。
- 如果需要居中的元素已经在高度上超过了视口，那它的顶部会被视口裁切掉。有一些办法可以绕过这个问题，但 hack 味道过浓。
- 在某些浏览器中，这个方法可能会导致元素的显示有一些模糊，因为元素可能被放置在半个像素上。这个问题可以用 `transform-style: preserve-3d` 来修复，不过这个修复手段也可以认为是一个
  hack，而且很难保证它在未来不会出问题。

```css
main {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

## 视口单位

只适用于在视口中居中的场景。

```css
main {
  width: 18em;
  padding: 1em 1.5em;
  margin: 50vh auto 0;
  transform: translateY(-50%);
}
```

[demo](play.csssecrets.io/vertical-centering-vh)

## Flexbox

当我们使用 Flexbox 时，`margin: auto` 不仅在水平方向上将元素居中，垂直方向上也是如此。

```css
body {
  display: flex;
  min-height: 100vh;
  margin: 0;
}
main {
  margin: auto;
}
```

Flexbox 的另一个好处在于，它还可以将匿名容器（即没有被标签包裹的文本节点）垂直居中。

```css
main {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 18em;
  height: 10em;
}
```

[demo](play.csssecrets.io/vertical-centering)

## 参考资料

- 《css 揭秘》
- [【译】盘点 8 种 CSS 实现垂直居中水平居中的绝对定位居中技术](https://blog.csdn.net/freshlover/article/details/11579669)
- [CSS 垂直水平完全居中手册](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651551685&idx=1&sn=92e8939d229aad8461f5a34cc5a3da17&chksm=8025a004b7522912138ec84f6e8f76e9d7033c9c33d072c62a87a097136b5551b73556f6040f&scene=0#rd)
- [CSS 布局奇技淫巧：各种居中](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651551442&idx=2&sn=7999c1f03f70b87f74b7b2b036657cc9&chksm=8025a113b752280504ecec7e7783122e2c07bd5c0bc56c94a4f1f8bc461db6476b080a7c0151&scene=0#rd)
