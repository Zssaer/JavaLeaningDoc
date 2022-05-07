# 在 CSS 中制作“拉起窗帘”效果

## 前言

“拉开窗帘”即背景在滚动时从暗变为亮，而顶部的内容在处于粘性位置时也会从亮变为暗，就像拉开窗帘一样。

![](../picture/2022-5-7%2016-16-04.gif)

这种效果在各个大型企业的宣传页基本上都有用到过。

## 教程

对于这种效果，看起来需要技术，其实根本不需要JS内容，使用HTML 和 CSS 就可以实现。

### 设置需要的元素

为了实现“拉起窗帘”这个效果，我们需要至少三个元素来实现。

```html
<div class="curtain">
  <div class="invert">
    <h2>Section title</h2>
  </div>
</div>
```

首先，我们需要一个用于放置“窗帘”的div，我们将给它赋予`.curtain`。

然后，在 其“窗帘”内部，我们还有一个`.invert` div子元素，它将作为我们的“粘性“框，用来承载我们需要在窗帘上面展示的内容。

最后，我们在这个里放置了一个h2标签内容。

### 为元素设置样式

为了便捷和统一性，我们先设置几个全局CSS变量，后面直接调用值就行。

```css
:root {
  --minh: 100vh;
  --color1: wheat;
  --color2: midnightblue;
}
```

我们先将其“窗帘”div元素设置上相关样式：

```css
.curtain {
  background-image: linear-gradient(to bottom, var(--color2) 50%, var(--color1) 50%);
}

/** 在元素后面添加空白元素,用于扩充其div大小 **/
.curtain::after {
  content: "";
  display: block;
  min-height: var(--minh);
}
```

这里我们使用linear-gradient 为其div元素从上到下设置了两款背景色。随后还需要在其after伪元素后添加空白内容，用作支撑出整个屏幕大小。

这时就可以滚动页面就会初步呈现于一个“拉起窗帘”效果。



随后我们设置“粘性”框相关样式：

```css
.invert {
  /** 设置为粘性固定位置 **/
  position: sticky;
  top: 20px;

  /** 元素内容混合设置为difference **/
  mix-blend-mode: difference;

  /** 使用flex布局置中 **/
  display: flex;
  align-items: center;
  justify-content: center;
  
  /** 设置最小高度 **/
  min-height: var(--minh);
}
```

其中设置sticky，和flex置中不用多说，这是经常使用的css。

而其中的mix-blend-mode是这个设计的重点，它将我们的内容与背景融合在一起。它拥有很多中融合方式，详细可以在https://css-tricks.com/almanac/properties/m/mix-blend-mode/学习一下。它的效果就像Photoshop里面的图层融合拥有。

这时我们将元素置中了后，但其实里面的H2没有设置颜色，所以为了使混合工作，我们需要设置标题的颜色。在这种情况下，我们将浅色值 ( `wheat`)分配给`--color1`变量。

```css
h2 {
	color: var(--color1);
}
```

这时就完成了整个效果了。

![](../picture/2022-5-7%2016-42-51.gif)









