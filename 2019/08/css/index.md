# CSS


CSS基本用法

<!--more-->

# CSS
- 不要有**多类症**，多用层叠来区分相同块的不同元素
- 不要有**多div症****，只有当没有象有元素能够实现区域分割的情况下使用div，div来区分不同意义或功能的条目（而不是他们的表现方法或布局）
- **div可以用来对块级元素进行分组，而span可以用来对行内元素进行分组或标识。**

## 选择器
```css
body
body h1
#btn
.btn
a:link
a:visited
a:hover
a:active
a:focus
*
#nav>li
h2 + p
acronym[title]
acronym[rel='nofollow']
```

## 层叠
- 标有`!important`的用户样式
- 标有`!important`的作者样式
- 作者样式
- 用户样式
- 浏览器/用户代理应用的样式

## 特殊性
a,b,c,d
- 如果样式是行内样式，那么a=1。
- b等于ID选择器的总数。
- c等于类、伪类和属性选择器的数量。
- d等于类型选择器和伪元素选择器的数量。

## 可视化格式模型
### 盒模型概述
盒模型是CSS的基石之一，它指定元素如何显示以及如何相互交互。页面上的每个元素被看做一个矩形框，这个框由元素的内容、内边距、边框和外边距组成
![](http://cdn.shanzei.top/20191030203257.png)

width和height指的是内容区域的宽度和高度。

增加内边距、边框和外边距不会影响内容区域的尺寸，但是会增加元素框的总尺寸。

```css
#myBox{
    margin: 10px;  /* 外边距 */
    padding: 5px;  /* 内边距 */
    width: 70px;
}
```

#### 外边距叠加
只有普通文档流中块框的垂直外边距才会发生外边距叠加。
行内框、浮动框或绝对定位框之间的外边距不会叠加

![](http://cdn.shanzei.top/20191030204238.png)
![](http://cdn.shanzei.top/20191030204306.png)
![](http://cdn.shanzei.top/20191030204537.png)

### 定位概述
#### 可视化格式模型
p、h1或div等元素常常称为块级元素，这些元素显示为一块内容，即“块框”

strong和span等元素称为行内元素，因为它们的内容显示在行中，即“行内框”

可以使用display属性改变生成的框的类型。

比如block表现得像块一样，或者none（没有框及其所以内容，不占用文档中的空间。）

CSS中有3种基本的定位机制：普通流、浮动和绝对定位。

普通流中元素框的位置由元素在HTML中的位置决定。

块级框从上到下一个接一个地垂直排列，框之间的垂直距离由框的垂直外边距计算出来。

行内框在一行中水平排列。

可以用水平内边距、边框和外边距调整它们的水平间距。但是，垂直内边距、边框和外边距不影响行内框的高度。修改行内框的唯一方法就是修改行高。
#### 相对定位
**可以通过设置垂直或水平位置，让这个元素“相对于”它的起点（即它本应该在的位置）移动 。**
![](http://cdn.shanzei.top/20191101114511.png)
相对定位实际上被看做普通流定位模型的一部分，因为元素的位置是相对于它在普通流中的位置的。
#### 绝对定位
**绝对定位使元素的位置与文档流无关，因此不占据空间。**
普通流中其他元素的布局就像绝对定位的元素不存在一样。
![](http://cdn.shanzei.top/20191101114751.png)

**绝对定位的起点是距离它最近的那个已定位的祖先元素的起点。
如果没有，就是初始包含块的起点**

**因为绝对定位可以覆盖页面上的其他元素，所以可以通过设置z-index属性来控制这些框的叠放次序。z-index越高，框在栈中的位置就越高。**

##### 固定定位
固定定位是绝对定位的一种。差异在于固定元素的包含块是视口（viewport）。
可以使某一元素在页面滚动时一直出现在屏幕上的固定位置。
```css
    .abc{
      position:fixed;
      right:100px;
      bottom:100px;
      width:50px;
      height:50px;
      background-color:red;
      border:2px solid black;
    }
```

#### 浮动定位
浮动的框可以左右移动，直到它的外边缘碰到包含框或另一个浮动框的边缘。

**浮动框也不在普通流中，普通流中的块框看不见浮动框**
![](http://cdn.shanzei.top/20191101121741.png)

![](http://cdn.shanzei.top/20191101121813.png)

![](http://cdn.shanzei.top/20191101122513.png)

## 背景图像效果
### 背景图像基础
![](http://cdn.shanzei.top/20191102134641.png)
![](http://cdn.shanzei.top/20191102134012.png)
![](http://cdn.shanzei.top/20191102134937.png)
![](http://cdn.shanzei.top/20191102135022.png)

### 圆角框
使用`border-radius`来设置圆角框
`border-radius`的值最好是width或height的一半

border-radius: 20px; 这句声明是实现圆角的W3C标准语法，它指定所有四个圆角的半径是20像素。该语法目前可被Opera、Chrome、Safari 5和IE 9支持。

Firefox和Safari 4及之前的版本则分别使用-moz-border-radius 和-webkit-border-radius 属性。

### 投影
工作原理是：将一个打的投影图像应用于容器div的背景，如何使用负的外边距偏移这个图像，从而显示出投影。
```css
-moz-box-shadow: 1px 1px 2px hsla(0,0%,0%,.3);
-webkit-box-shadow: 1px 1px 2px hsla(0,0%,0%,.3);
box-shadow: 1px 1px 2px hsla(0,0%,0%,.3);
```

### 利用RGBA或HSLA实现半透明背景
RGBA(Red-Green-Blue-Alpha)
HSLA(Hue-Saturation-Lightness-Alpha)

```CSS
background-color: hsla(182, 44%, 76%, .5)
background-color: rgba(255, 255, 255, .5)
```
