## CSS 面试知识点总结

本部分主要是笔者在复习 CSS 相关知识和一些相关面试题时所做的笔记，如果出现错误，希望大家指出！

### 一.基础

#### 1.介绍一下标准的 CSS 的盒子模型？低版本 IE 的盒子模型有什么不同的？

> CSS3 中的盒模型有以下两种：**标准盒模型**、**IE（替代）盒模型**。
>
> 两种盒子模型都是由 `content + padding + border + margin` 构成，其大小都是由 `content + padding + border` 决定的，但是盒子内容宽/高度（即 `width/height`）的计算范围根据盒模型的不同会有所不同：
>
> - 标准盒模型：只包含 `content` 。
> - IE（替代）盒模型：`content + padding + border` 。
>
> 可以通过 `box-sizing` 来改变元素的盒模型：
>
> - `box-sizing: content-box` ：标准盒模型（默认值）。
> - `box-sizing: border-box` ：IE（替代）盒模型。

回答：

```
盒模型都是由四个部分组成的，分别是margin、border、padding和content。

标准盒模型和IE盒模型的区别在于设置width和height时，所对应的范围不同。标准盒模型的width和height属性的
范围只包含了content，而IE盒模型的width和height属性的范围包含了border、padding和content。

一般来说，我们可以通过修改元素的box-sizing属性来改变元素的盒模型。
```

详细的资料可以参考：
[《CSS 盒模型详解》](https://juejin.im/post/59ef72f5f265da4320026f76)

#### 2.CSS 选择器和优先级

![image-20220307204813508](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220307204813508.png)

> 对于选择器的**优先级**：
>
> - 标签选择器、伪元素选择器：1
> - 类选择器、伪类选择器、属性选择器：10
> - id 选择器：100
> - 内联样式：1000
>
> **注意事项：**
>
> - !important声明的样式的优先级最高；
> - 如果优先级相同，则最后出现的样式生效；
> - 继承得到的样式的优先级最低；
> - 通用选择器（*）、子选择器（>）和相邻同胞选择器（+）并不在这四个等级中，所以它们的权值都为 0 ；
> - 样式表的来源不同时，优先级顺序为：内联样式 > 内部样式 > 外部样式 > 浏览器用户自定义样式 > 浏览器默认样式。

[《CSS 优先级计算及应用》](https://www.jianshu.com/p/1c4e639ff7d5)
[《CSS 优先级计算规则》](http://www.cnblogs.com/wangmeijian/p/4207433.html)
[《有趣：256 个 class 选择器可以干掉 1 个 id 选择器》](https://www.zhangxinxu.com/wordpress/2012/08/256-class-selector-beat-id-selector/)

#### 3.::before 和:after 中双冒号和单冒号有什么区别？解释一下这 2 个伪元素的作用。

相关知识点：

```
单冒号（:）用于CSS3伪类，双冒号（::）用于CSS3伪元素。（伪元素由双冒号和伪元素名称组成）
双冒号是在当前规范中引入的，用于区分伪类和伪元素。不过浏览器需要同时支持旧的已经存在的伪元素写法，
比如:first-line、:first-letter、:before、:after等，
而新的在CSS3中引入的伪元素则不允许再支持旧的单冒号的写法。

想让插入的内容出现在其它内容前，使用::before，否者，使用::after；
在代码顺序上，::after生成的内容也比::before生成的内容靠后。
如果按堆栈视角，::after生成的内容会在::before生成的内容之上。
```

回答：

```
在css3中使用单冒号来表示伪类，用双冒号来表示伪元素。但是为了兼容已有的伪元素的写法，在一些浏览器中也可以使用单冒号
来表示伪元素。

伪类一般匹配的是元素的一些特殊状态，如hover、link等，而伪元素一般匹配的特殊的位置，比如after、before等。
```

#### 4.伪类与伪元素的区别

> - 伪元素：在内容元素的前后插入额外的元素或样式，但是这些元素实际上并不在文档中生成。它们只在外部显示可见，但不会在文档的源代码中找到它们，因此，称为“伪”元素。例如：
>
> ```css
> p::before {content:"第一章：";}
> p::after {content:"Hot!";}
> p::first-line {background:red;}
> p::first-letter {font-size:30px;}
> ```
>
> - 伪类：将特殊的效果添加到特定选择器上。它是已有元素上添加类别的，不会产生新的元素。例如：
>
> ```css
> a:hover {color: #FF00FF}
> p:first-child {color: red}
> ```

```
css引入伪类和伪元素概念是为了格式化文档树以外的信息。也就是说，伪类和伪元素是用来修饰不在文档树中的部分，比如，一句
话中的第一个字母，或者是列表中的第一个元素。

伪类用于当已有的元素处于某个状态时，为其添加对应的样式，这个状态是根据用户行为而动态变化的。比如说，当用户悬停在指定的
元素时，我们可以通过:hover来描述这个元素的状态。

伪元素用于创建一些不在文档树中的元素，并为其添加样式。它们允许我们为元素的某些部分设置样式。比如说，我们可以通过::be
fore来在一个元素前增加一些文本，并为这些文本添加样式。虽然用户可以看到这些文本，但是这些文本实际上不在文档树中。

有时你会发现伪元素使用了两个冒号（::）而不是一个冒号（:）。这是CSS3的一部分，并尝试区分伪类和伪元素。大多数浏览
器都支持这两个值。按照规则应该使用（::）而不是（:），从而区分伪类和伪元素。但是，由于在旧版本的W3C规范并未对此进行
特别区分，因此目前绝大多数的浏览器都支持使用这两种方式表示伪元素。
```

详细资料可以参考：
[《总结伪类与伪元素》](http://www.alloyteam.com/2016/05/summary-of-pseudo-classes-and-pseudo-elements/)

> #### 伪类
>
> **条件伪类**
>
> - `:lang()`：基于元素语言来匹配页面元素；
> - `:dir()`：匹配特定文字书写方向的元素；
> - `:has()`：匹配包含指定元素的元素；
> - `:is()`：匹配指定选择器列表里的元素；
> - `:not()`：用来匹配不符合一组选择器的元素；
>
> **行为伪类**
>
> - `:active`：鼠标激活的元素；
> - `:hover`：	鼠标悬浮的元素；
> - `::selection`：鼠标选中的元素；
>
> **状态伪类**
>
> - `:target`：当前锚点的元素；
> - `:link`：未访问的链接元素；
> - `:visited`：已访问的链接元素；
> - `:focus`：输入聚焦的表单元素；
> - `:required`：输入必填的表单元素；
> - `:valid`：输入合法的表单元素；
> - `:invalid`：输入非法的表单元素；
> - `:in-range`：输入范围以内的表单元素；
> - `:out-of-range`：输入范围以外的表单元素；
> - `:checked`：选项选中的表单元素；
> - `:optional`：选项可选的表单元素；
> - `:enabled`：事件启用的表单元素；
> - `:disabled`：事件禁用的表单元素；
> - `:read-only`：只读的表单元素；
> - `:read-write`：可读可写的表单元素；
> - `:blank`：输入为空的表单元素；
> - `:current()`：浏览中的元素；
> - `:past()`：已浏览的元素；
> - `:future()`：未浏览的元素；
>
> **结构伪类**
>
> - `:root`：文档的根元素；
> - `:empty`：无子元素的元素；
> - `:first-letter`：元素的首字母；
> - `:first-line`：元素的首行；
> - `:nth-child(n)`：元素中指定顺序索引的元素；
> - `:nth-last-child(n)`：元素中指定逆序索引的元素；；
> - `:first-child	`：元素中为首的元素；
> - `:last-child`	：元素中为尾的元素；
> - `:only-child`：父元素仅有该元素的元素；
> - `:nth-of-type(n)	`：标签中指定顺序索引的标签；
> - `:nth-last-of-type(n)`：标签中指定逆序索引的标签；
> - `:first-of-type`	：标签中为首的标签；
> - `:last-of-type`：标签中为尾标签；
> - `:only-of-type`：父元素仅有该标签的标签；

#### 5.CSS中可继承与不可继承属性有哪些

> 在 CSS 中有一个很重要的特性就是子元素会继承父元素对应属性计算后的值。比如页面根元素 html 的文本颜色默认是黑色的，页面中的所有其他元素都将继承这个颜色，当申明了如下样式后，H1 文本将变成橙色。
>
> ```css
> body {
>     color: orange;
> }
> h1 {
>     color: inherit;
> }
> ```
>
> 设想一下，如果 CSS 中不存在继承性，那么我们就需要为不同文本的标签都设置一下 color，这样一来的后果就是 CSS 的文件大小就会无限增大。
>
> CSS 属性很多，但并不是所有的属性默认都是能继承父元素对应属性的，那**哪些属性存在默认继承的行为呢？一定是那些不会影响到页面布局的属性**
>
> 对于其他默认不继承的属性也可以通过以下几个属性值来控制继承行为：
>
> - `inherit`：继承父元素对应属性的计算值；
> - `initial`：应用该属性的默认值，比如 color 的默认值是 `#000`；
> - `unset`：如果属性是默认可以继承的，则取 `inherit` 的效果，否则同 `initial`；
> - `revert`：效果等同于 `unset`，兼容性差。

> **一、无继承性的属性**
>
> 1. **display**：规定元素应该生成的框的类型
> 2. **文本属性**：
>
> - vertical-align：垂直文本对齐
> - text-decoration：规定添加到文本的装饰
> - text-shadow：文本阴影效果
> - white-space：空白符的处理
> - unicode-bidi：设置文本的方向
>
> 3. **盒子模型的属性**：width、height、margin、border、padding
>
> 4. **背景属性**：background、background-color、background-image、background-repeat、background-position、background-attachment
>
> 5. **定位属性**：float、clear、position、top、right、bottom、left、min-width、min-height、max-width、max-height、overflow、clip、z-index
>
> 6. **生成内容属性**：content、counter-reset、counter-increment
>
> 7. **轮廓样式属性**：outline-style、outline-width、outline-color、outline
>
> 8. **页面样式属性**：size、page-break-before、page-break-after
>
> 9. **声音样式属性**：pause-before、pause-after、pause、cue-before、cue-after、cue、play-during
>
> **二、有继承性的属性**
>
> 1. **字体系列属性**
>
> - font-family：字体系列
> - font-weight：字体的粗细
> - font-size：字体的大小
> - font-style：字体的风格
>
> 2. **文本系列属性**
>
> - text-indent：文本缩进
> - text-align：文本水平对齐
> - line-height：行高
> - word-spacing：单词之间的间距
> - letter-spacing：中文或者字母之间的间距
> - text-transform：控制文本大小写（就是uppercase、lowercase、capitalize这三个）
> - color：文本颜色
>
> 3. **元素可见性**
>
> - visibility：控制元素显示隐藏
>
> 4. **列表布局属性**
>
> - list-style：列表风格，包括list-style-type、list-style-image等
>
> 5. **光标属性**
>
> - cursor：光标显示为何种形态

相关资料：

```
每个CSS属性定义的概述都指出了这个属性是默认继承的，还是默认不继承的。这决定了当你没有为元素的属性指定值时该如何计算
值。

当元素的一个继承属性没有指定值时，则取父元素的同属性的计算值。只有文档根元素取该属性的概述中给定的初始值（这里的意思应
该是在该属性本身的定义中的默认值）。

当元素的一个非继承属性（在Mozilla code里有时称之为reset property）没有指定值时，则取属性的初始值initial v
alue（该值在该属性的概述里被指定）。

注意：当一个属性不是继承属性时，可以使用inherit关键字指定一个属性应从父元素继承它的值，inherit关键字用于显式地
指定继承性，可用于任何继承性/非继承性属性。
```

回答：

```
每一个属性在定义中都给出了这个属性是否具有继承性，一个具有继承性的属性会在没有指定值的时候，会使用父元素的同属性的值
来作为自己的值。

一般具有继承性的属性有，字体相关的属性，font-size和font-weight等。文本相关的属性，color和text-align等。
表格的一些布局属性、列表属性如list-style等。还有光标属性cursor、元素可见性visibility。

当一个属性不是继承属性的时候，我们也可以通过将它的值设置为inherit来使它从父元素那获取同名的属性值来继承。
```

详细的资料可以参考：
[《继承属性》](https://developer.mozilla.org/zh-CN/docs/Web/CSS/inheritance)
[《CSS 有哪些属性可以继承？》](https://www.jianshu.com/p/34044e3c9317)

#### 6.display的属性值及其作用

![image-20220307212210282](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220307212210282.png)

#### 7.关于伪类 LVHA 的解释?

```
a标签有四种状态：链接访问前、链接访问后、鼠标滑过、激活，分别对应四种伪类:link、:visited、:hover、:active；

当链接未访问过时：

（1）当鼠标滑过a链接时，满足:link和:hover两种状态，要改变a标签的颜色，就必须将:hover伪类在:link伪
类后面声明；
（2）当鼠标点击激活a链接时，同时满足:link、:hover、:active三种状态，要显示a标签激活时的样式（:active），
必须将:active声明放到:link和:hover之后。因此得出LVHA这个顺序。

当链接访问过时，情况基本同上，只不过需要将:link换成:visited。

这个顺序能不能变？可以，但也只有:link和:visited可以交换位置，因为一个链接要么访问过要么没访问过，不可能同时满足，
也就不存在覆盖的问题。
```

#### 8.CSS3 新增伪类有那些？

```
（1）elem:nth-child(n)选中父元素下的第n个子元素，并且这个子元素的标签名为elem，n可以接受具体的数
值，也可以接受函数。

（2）elem:nth-last-child(n)作用同上，不过是从后开始查找。

（3）elem:last-child选中最后一个子元素。

（4）elem:only-child如果elem是父元素下唯一的子元素，则选中之。

（5）elem:nth-of-type(n)选中父元素下第n个elem类型元素，n可以接受具体的数值，也可以接受函数。

（6）elem:first-of-type选中父元素下第一个elem类型元素。

（7）elem:last-of-type选中父元素下最后一个elem类型元素。

（8）elem:only-of-type如果父元素下的子元素只有一个elem类型元素，则选中该元素。

（9）elem:empty选中不包含子元素和内容的elem类型元素。

（10）elem:target选择当前活动的elem元素。

（11）:not(elem)选择非elem元素的每个元素。

（12）:enabled 控制表单控件的禁用状态。

（13）:disabled	控制表单控件的禁用状态。

(14):checked单选框或复选框被选中。

```

详细的资料可以参考：
[《CSS3 新特性总结(伪类)》](https://www.cnblogs.com/SKLthegoodman/p/css3.html)
[《浅谈 CSS 伪类和伪元素及 CSS3 新增伪类》](https://blog.csdn.net/zhouziyu2011/article/details/58605705)

#### 9.display的block、inline和inline-block的区别

> （1）**block：** 会独占一行，多个元素会另起一行，可以设置width、height、margin和padding属性；
>
> （2）**inline：** 元素不会独占一行，设置width、height属性无效。但可以设置水平方向的margin和padding属性，不能设置垂直方向的padding和margin；
>
> （3）**inline-block：** 将对象设置为inline对象，但对象的内容作为block对象呈现，之后的内联对象会被排列在同一行内。
>
> 对于行内元素和块级元素，其特点如下：
>
> **（1）行内元素**
>
> - 设置宽高无效；
> - 可以设置水平方向的margin和padding属性，不能设置垂直方向的padding和margin；
> - 不会自动换行；
>
> **（2）块级元素**
>
> - 可以设置宽高；
> - 设置margin和padding都有效；
> - 可以自动换行；
> - 多个块状，默认排列从上到下。

详细资料可以参考：
[《CSS display 属性》](http://www.w3school.com.cn/css/pr_class_display.asp)

#### 10.@规则

> CSS 规则是样式表的主体，通常样式表会包括大量的规则列表。但有时候也需要在样式表中包括其他的一些信息，比如字符集，导入其它的外部样式表，字体等，这些需要专门的语句表示。
>
> 而 @规则 就是这样的语句。CSS 里包含了以下 @规则：
>
> - [@namespace](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40namespace) 告诉 CSS 引擎必须考虑XML命名空间。
> - [@media](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40media), 如果满足媒体查询的条件则条件规则组里的规则生效。
> - [@page](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40page), 描述打印文档时布局的变化.
> - [@font-face](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40font-face), 描述将下载的外部的字体。
> - [@keyframes](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40keyframes), 描述 CSS 动画的关键帧。
> - [@document](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40document), 如果文档样式表满足给定条件则条件规则组里的规则生效。 (推延至 CSS Level 4 规范)
>
> 除了以上这几个之外，下面还将对几个比较生涩的 @规则 进行介绍。
>
> #### @charset
>
> [@charset](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40charset) 用于定义样式表使用的字符集。它必须是样式表中的第一个元素。如果有多个 `@charset` 被声明，只有第一个会被使用，而且不能在HTML元素或HTML页面的 `<style>` 元素内使用。
>
> 注意：值必须是双引号包裹，且和
>
> ```css
> @charset "UTF-8";
> ```
>
> 平时写样式文件都没写 @charset 规则，那这个 CSS 文件到底是用的什么字符编码的呢？
>
> 某个样式表文件到底用的是什么字符编码，浏览器有一套识别顺序（优先级由高到低）：
>
> - 文件开头的 [Byte order mark](https://link.juejin.cn?target=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FByte_order_mark) 字符值，不过一般编辑器并不能看到文件头里的 BOM 值；
>
> - HTTP 响应头里的 `content-type` 字段包含的 `charset` 所指定的值，比如：
>
>   ```
>   Content-Type: text/css; charset=utf-8
>   ```
>
> - CSS 文件头里定义的 @charset 规则里指定的字符编码；
>
> - `<link>` 标签里的 charset 属性，该条已在 HTML5 中废除；
>
> - 默认是 `UTF-8`。
>
> #### @import
>
> [@import](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40import) 用于告诉 CSS 引擎引入一个外部样式表。
>
> link 和 @import 都能导入一个样式文件，它们有什么区别嘛？
>
> - link 是 HTML 标签，除了能导入 CSS 外，还能导入别的资源，比如图片、脚本和字体等；而 @import 是 CSS 的语法，只能用来导入 CSS；
> - link 导入的样式会在页面加载时同时加载，@import 导入的样式需等页面加载完成后再加载；
> - link 没有兼容性问题，@import 不兼容 ie5 以下；
> - link 可以通过 JS 操作 DOM 动态引入样式表改变样式，而@import不可以。
>
> #### @supports
>
> [@supports](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40supports) 用于查询特定的 CSS 是否生效，可以结合 not、and 和 or 操作符进行后续的操作。
>
> ```css
> /* 如果支持自定义属性，则把 body 颜色设置为变量 varName 指定的颜色 */
> @supports (--foo: green) {
>     body {
>         color: var(--varName);
>     }
> }
> ```

#### 11.CSS3 有哪些新特性？

> * 新增各种CSS选择器 （: not(.input)：所有 class 不是“input”的节点）
>* 圆角 （border-radius:8px）
> * 多列布局 （multi-column layout）
>* 阴影和反射 （Shadoweflect）
> * 文字特效 （text-shadow）
>* 文字渲染 （Text-decoration）
> * 线性渐变 （gradient）
>* 旋转 （transform）
> * 增加了旋转,缩放,定位,倾斜,动画,多背景

#### 12.文档流

> 在 CSS 的世界中，会把内容按照从左到右、从上到下的顺序进行排列显示。正常情况下会把页面分割成一行一行的显示，而每行又可能由多列组成，所以从视觉上看起来就是从上到下从左到右，而这就是 CSS 中的流式布局，又叫文档流。文档流就像水一样，能够自适应所在的容器，一般它有如下几个特性：
>
> - 块级元素默认会占满整行，所以多个块级盒子之间是从上到下排列的；
> - 内联元素默认会在一行里一列一列的排布，当一行放不下的时候，会自动切换到下一行继续按照列排布；
>
> **如何脱离文档流呢？**
>
> 脱流文档流指节点脱流正常文档流后，在正常文档流中的其他节点将忽略该节点并填补其原先空间。文档一旦脱流，计算其父节点高度时不会将其高度纳入，脱流节点不占据空间。有两种方式可以让元素脱离文档流：浮动和定位。
>
> - 使用浮动（float）会将元素脱离文档流，移动到容器左/右侧边界或者是另一个浮动元素旁边，该浮动元素之前占用的空间将被别的元素填补，另外浮动之后所占用的区域不会和别的元素之间发生重叠；
> - 使用绝对定位（`position: absolute;`）或者固定定位（`position: fixed;`）也会使得元素脱离文档流，且空出来的位置将自动被后续节点填补。

#### 14.经常遇到的浏览器的兼容性有哪些？原因，解决方法是什么，常用 hack 的技巧？

```
（1）png24位的图片在iE6浏览器上出现背景
解决方案：做成PNG8，也可以引用一段脚本处理。

（2）浏览器默认的margin和padding不同
解决方案：加一个全局的*{margin:0;padding:0;}来统一。

（3）IE6双边距bug：在IE6下，如果对元素设置了浮动，同时又设置了margin-left或
margin-right，margin值会加倍。

#box{float:left;width:10px;margin:0 0 0 10px;}

这种情况之下IE会产生20px的距离
解决方案：在float的标签样式控制中加入_display:inline;将其转化为行内属性。(_这个符号只有ie6会识别)

（4）渐进识别的方式，从总体中逐渐排除局部。
首先，巧妙的使用"\9"这一标记，将IE游览器从所有情况中分离出来。
接着，再次使用"+"将IE8和IE7、IE6分离开来，这样IE8已经独立识别。
.bb{
background-color:#f1ee18;/*所有识别*/
.background-color:#00deff\9;/*IE6、7、8识别*/
+background-color:#a200ff;/*IE6、7识别*/
_background-color:#1e0bd1;/*IE6识别*/
}

（5）IE下，可以使用获取常规属性的方法来获取自定义属性，也可以使用getAttribute()获取自定义
属性；Firefox下，只能使用getAttribute()获取自定义属性
解决方法：统一通过getAttribute()获取自定义属性。

（6）IE下，event对象有x、y属性，但是没有pageX、pageY属性;Firefox下，event对象有
pageX、pageY属性，但是没有x、y属性。
解决方法：（条件注释）缺点是在IE浏览器下可能会增加额外的HTTP请求数。

（7）Chrome中文界面下默认会将小于12px的文本强制按照12px显示
解决方法：

1.可通过加入CSS属性-webkit-text-size-adjust:none;解决。但是，在chrome
更新到27版本之后就不可以用了。

2.还可以使用-webkit-transform:scale(0.5);注意-webkit-transform:scale(0.75);
收缩的是整个span的大小，这时候，必须要将span转换成块元素，可以使用display：block/inline-block/...；

（8）超链接访问过后hover样式就不出现了，被点击访问过的超链接样式不再具有hover和active了
解决方法：改变CSS属性的排列顺序L-V-H-A

（9）怪异模式问题：漏写DTD声明，Firefox仍然会按照标准模式来解析网页，但在IE中会触发怪异模
式。为避免怪异模式给我们带来不必要的麻烦，最好养成书写DTD声明的好习惯。
```

#### 15.li 与 li 之间有看不见的空白间隔是什么原因引起的？有什么解决办法？

> 浏览器会把inline内联元素间的空白字符（空格、换行、Tab等）渲染成一个空格。为了美观，通常是一个`<li>`放在一行，这导致`<li>`换行后产生换行字符，它变成一个空格，占用了一个字符的宽度。
>
> **解决办法：**
>
> （1）为`<li>`设置float:left。不足：有些容器是不能设置浮动，如左右切换的焦点图等。
>
> （2）将所有`<li>`写在同一行。不足：代码不美观。
>
> （3）将`<ul>`内的字符尺寸直接设为0，即font-size:0。不足：`<ul>`中的其他字符尺寸也被设为0，需要额外重新设定其他字符尺寸，且在Safari浏览器依然会出现空白间隔。
>
> （4）消除`<ul>`的字符间隔letter-spacing:-8px，不足：这也设置了`<li>`内的字符间隔，因此需要将`<li>`内的字符间隔设为默认letter-spacing:normal。

详细资料可以参考：
[《li 与 li 之间有看不见的空白间隔是什么原因引起的？》](https://blog.csdn.net/sjinsa/article/details/70919546)

#### 16.为什么要初始化 CSS 样式？

```
-因为浏览器的兼容问题，不同浏览器对有些标签的默认值是不同的，如果没对CSS初始化往往会出现浏览器之间的页面显示差异。

-当然，初始化样式会对SEO有一定的影响，但鱼和熊掌不可兼得，但力求影响最小的情况下初始化。

最简单的初始化方法：*{padding:0;margin:0;}（强烈不建议）

淘宝的样式初始化代码：
body,h1,h2,h3,h4,h5,h6,hr,p,blockquote,dl,dt,dd,ul,ol,li,pre,form,fieldset,legend
,button,input,textarea,th,td{margin:0;padding:0;}
body,button,input,select,textarea{font:12px/1.5tahoma,arial,\5b8b\4f53;}
h1,h2,h3,h4,h5,h6{font-size:100%;}
address,cite,dfn,em,var{font-style:normal;}
code,kbd,pre,samp{font-family:couriernew,courier,monospace;}
small{font-size:12px;}
ul,ol{list-style:none;}
a{text-decoration:none;}
a:hover{text-decoration:underline;}
sup{vertical-align:text-top;}
sub{vertical-align:text-bottom;}
legend{color:#000;}
fieldset,img{border:0;}
button,input,select,textarea{font-size:100%;}
table{border-collapse:collapse;border-spacing:0;}
```

#### 17.什么是包含块，对于包含块的理解?

```
包含块（containing block）就是元素用来计算和定位的一个框。

（1）根元素（很多场景下可以看成是<html>）被称为“初始包含块”，其尺寸等同于浏览器可视窗口的大小。

（2）对于其他元素，如果该元素的position是relative或者static，则“包含块”由其最近的块容器祖先盒的content box
边界形成。

（3）如果元素position:fixed，则“包含块”是“初始包含块”。

（4）如果元素position:absolute，则“包含块”由最近的position不为static的祖先元素建立，具体方式如下：

如果该祖先元素是纯inline元素，则规则略复杂：
•假设给内联元素的前后各生成一个宽度为0的内联盒子（inline box），则这两个内联盒子的padding box外面的包
围盒就是内联元素的“包含块”；
•如果该内联元素被跨行分割了，那么“包含块”是未定义的，也就是CSS2.1规范并没有明确定义，浏览器自行发挥
否则，“包含块”由该祖先的padding box边界形成。

如果没有符合条件的祖先元素，则“包含块”是“初始包含块”。
```

#### 18.CSS 里的 visibility 属性有个 collapse 属性值是干嘛用的？在不同浏览器下以后什么区别？

```
（1）对于一般的元素，它的表现跟visibility：hidden;是一样的。元素是不可见的，但此时仍占用页面空间。

（2）但例外的是，如果这个元素是table相关的元素，例如table行，table group，table列，table column group，它的
表现却跟display:none一样，也就是说，它们占用的空间也会释放。

在不同浏览器下的区别：

在谷歌浏览器里，使用collapse值和使用hidden值没有什么区别。

在火狐浏览器、Opera和IE11里，使用collapse值的效果就如它的字面意思：table的行会消失，它的下面一行会补充它的位
置。

```

详细资料可以参考：
[《CSS 里的 visibility 属性有个鲜为人知的属性值：collapse》](http://www.webhek.com/post/visibility-collapse.html)

#### 19.width:auto 和 width:100%的区别

```
一般而言

width:100%会使元素box的宽度等于父元素的content box的宽度。

width:auto会使元素撑满整个父元素，margin、border、padding、content区域会自动分配水平空间。
```

#### 20.视觉格式化模型

> 视觉格式化模型（Visual formatting model）是用来处理和在视觉媒体上显示文档时使用的计算规则。CSS 中一切皆盒子，而视觉格式化模型简单来理解就是规定这些盒子应该怎么样放置到页面中去，这个模型在计算的时候会依赖到很多的因素，比如：盒子尺寸、盒子类型、定位方案（是浮动还是定位）、兄弟元素或者子元素以及一些别的因素。
>
> 盒子类型由 display 决定，同时给一个元素设置 display 后，将会决定这个盒子的 2 个显示类型（display type）：
>
> - outer display type（对外显示）：决定了该元素本身是如何布局的，即参与何种格式化上下文；
> - inner display type（对内显示）：其实就相当于把该元素当成了容器，规定了其内部子元素是如何布局的，参与何种格式化上下文；
>
> #### outer display type
>
> 对外显示方面，盒子类型可以分成 2 类：block-level box（块级盒子） 和 inline-level box（行内级盒子）。
>
> 依据上图可以列出都有哪些块级和行内级盒子：
>
> - 块级盒子：display 为 block、list-item、table、flex、grid、flow-root 等；
> - 行内级盒子：display 为 inline、inline-block、inline-table 等；
>
> 所有块级盒子都会参与 BFC，呈现垂直排列；而所有行内级盒子都参会 IFC，呈现水平排列。
>
> 除此之外，block、inline 和 inline-block 还有什么更具体的区别呢？
>
> **block**
>
> - 占满一行，默认继承父元素的宽度；多个块元素将从上到下进行排列；
> - 设置 width/height 将会生效；
> - 设置 padding 和 margin 将会生效；
>
> **inline**
>
> - 不会占满一行，宽度随着内容而变化；多个 inline 元素将按照从左到右的顺序在一行里排列显示，如果一行显示不下，则自动换行；
> - 设置 width/height 将不会生效；
> - 设置竖直方向上的 padding 和 margin 将不会生效；
>
> **inline-block**
>
> - 是行内块元素，不单独占满一行，可以看成是能够在一行里进行左右排列的块元素；
> - 设置 width/height 将会生效；
> - 设置 padding 和 margin 将会生效；
>
> #### inner display type
>
> 对内方面，其实就是把元素当成了容器，里面包裹着文本或者其他子元素。container box 的类型依据 display 的值不同，分为 4 种：
>
> - block container：建立 BFC 或者 IFC；
> - flex container：建立 FFC；
> - grid container：建立 GFC;
> - ruby container：接触不多，不做介绍。
>
> 值得一提的是如果把 img 这种替换元素（replaced element）申明为 block 是不会产生 container box 的，因为替换元素比如 img 设计的初衷就仅仅是通过 src 把内容替换成图片，完全没考虑过会把它当成容器。

#### 21.简单介绍使用图片 base64 编码的优点和缺点。

```
base64编码是一种图片处理格式，通过特定的算法将图片编码成一长串字符串，在页面上显示的时候，可以用该字符串来代替图片的
url属性。

使用base64的优点是：

（1）减少一个图片的HTTP请求

使用base64的缺点是：

（1）根据base64的编码原理，编码后的大小会比原文件大小大1/3，如果把大图片编码到html/css中，不仅会造成文件体
积的增加，影响文件的加载速度，还会增加浏览器对html或css文件解析渲染的时间。

（2）使用base64无法直接缓存，要缓存只能缓存包含base64的文件，比如HTML或者CSS，这相比域直接缓存图片的效果要
差很多。

（3）兼容性的问题，ie8以前的浏览器不支持。

一般一些网站的小图标可以使用base64图片来引入。
```

详细资料可以参考：
[《玩转图片 base64 编码》](https://www.cnblogs.com/coco1s/p/4375774.html)
[《前端开发中，使用 base64 图片的弊端是什么？》](https://www.zhihu.com/question/31155574)
[《小 tip:base64:URL 背景图片与 web 页面性能优化》](https://www.zhangxinxu.com/wordpress/2012/04/base64-url-image-%E5%9B%BE%E7%89%87-%E9%A1%B5%E9%9D%A2%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96/)

#### 22.格式化上下文

> 格式化上下文（Formatting Context）是 CSS2.1 规范中的一个概念，大概说的是页面中的一块渲染区域，规定了渲染区域内部的子元素是如何排版以及相互作用的。
>
> 不同类型的盒子有不同格式化上下文，大概有这 4 类：
>
> - BFC (Block Formatting Context) 块级格式化上下文；
> - IFC (Inline Formatting Context) 行内格式化上下文；
> - FFC (Flex Formatting Context) 弹性格式化上下文；
> - GFC (Grid Formatting Context) 格栅格式化上下文；
>
> 其中 BFC 和 IFC 在 CSS 中扮演着非常重要的角色，因为它们直接影响了网页布局，所以需要深入理解其原理。

#### 23.值和单位

> CSS 的声明是由属性和值组成的，而值的类型有许多种：
>
> - 数值：长度值 ，用于指定例如元素 width、border-width、font-size 等属性的值；
> - 百分比：可以用于指定尺寸或长度，例如取决于父容器的 width、height 或默认的 font-size；
> - 颜色：用于指定 background-color、color 等；
> - 坐标位置：以屏幕的左上角为坐标原点定位元素的位置，比如常见的 background-position、top、right、bottom 和 left 等属性；
> - 函数：用于指定资源路径或背景图片的渐变，比如 url()、linear-gradient() 等；
>
> 而还有些值是需要带单位的，比如 width: 100px，这里的 px 就是表示长度的单位，长度单位除了 px 外，比较常用的还有 em、rem、vw/vh 等。那他们有什么区别呢？又应该在什么时候使用它们呢？
>
> #### px
>
> 屏幕分辨率是指在屏幕的横纵方向上的像素点数量，比如分辨率 1920×1080 意味着水平方向含有 1920 个像素数，垂直方向含有 1080 个像素数。
>
> <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7469f30a44fb4211bb7860eb82787819~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp" alt="img" style="zoom:50%;" />
>
> 而 px 表示的是 CSS 中的像素，在 CSS 中它是绝对的长度单位，也是最基础的单位，其他长度单位会自动被浏览器换算成 px。但是对于设备而言，它其实又是相对的长度单位，比如宽高都为 2px，在正常的屏幕下，其实就是 4 个像素点，而在设备像素比(devicePixelRatio) 为 2 的 Retina 屏幕下，它就有 16 个像素点。所以屏幕尺寸一致的情况下，屏幕分辨率越高，显示效果就越细腻。
>
> 讲到这里，还有一些相关的概念需要理清下：
>
> **设备像素（Device pixels）**
>
> 设备屏幕的物理像素，表示的是屏幕的横纵有多少像素点；和屏幕分辨率是差不多的意思。
>
> **设备像素比（DPR）**
>
> 设备像素比表示 1 个 CSS 像素等于几个物理像素。
>
> 计算公式：DPR = 物理像素数 / 逻辑像素数；
>
> 在浏览器中可以通过 window.devicePixelRatio 来获取当前屏幕的 DPR。
>
> **像素密度（DPI/PPI）**
>
> 像素密度也叫显示密度或者屏幕密度，缩写为 DPI(Dots Per Inch) 或者 PPI(Pixel Per Inch)。从技术角度说，PPI 只存在于计算机显示领域，而 DPI 只出现于打印或印刷领域。
>
> 计算公式：像素密度 = 屏幕对角线的像素尺寸 / 物理尺寸
>
> 比如，对于分辨率为 750 * 1334 的 iPhone 6 来说，它的像素密度为：
>
> ```js
> Math.sqrt(750 * 750 + 1334 * 1334) / 4.7 = 326ppi
> ```
>
> **设备独立像素（DIP）**
>
> DIP 是特别针对 Android设备而衍生出来的，原因是安卓屏幕的尺寸繁多，因此为了显示能尽量和设备无关，而提出的这个概念。它是基于屏幕密度而计算的，认为当屏幕密度是 160 的时候，px = DIP。
>
> 计算公式：dip = px * 160 / dpi
>
> #### em
>
> em 是 CSS 中的相对长度单位中的一个。居然是相对的，那它到底是相对的谁呢？它有 2 层意思：
>
> - 在 font-size 中使用是相对于**父元素**的 font-size 大小，比如父元素 font-size: 16px，当给子元素指定 font-size: 2em 的时候，经过计算后它的字体大小会是 32px；
> - 在其他属性中使用是相对于自身的字体大小，如 width/height/padding/margin 等；
>
> 我们都知道每个浏览器都会给 HTML 根元素 html 设置一个默认的 font-size，而这个值通常是 16px。这也就是为什么 1em = 16px 的原因所在了。
>
> em 在计算的时候是会层层计算的，比如：
>
> ```html
> <div>
>     <p></p>
> </div>
> div { font-size: 2em; }
> p { font-size: 2em; }
> ```
>
> 对于如上一个结构的 HTML，由于根元素 html 的字体大小是 16px，所以 p 标签最终计算出来后的字体大小会是 16 * 2 * 2 = 64px
>
> #### rem
>
> rem(root em) 和 em 一样，也是一个相对长度单位，不过 rem 相对的是 HTML 的根元素 html。
>
> rem 由于是**基于 html 的 font-size **来计算，所以通常用于自适应网站或者 H5 中。
>
> 比如在做 H5 的时候，前端通常会让 UI 给 750px 宽的设计图，而在开发的时候可以基于 iPhone X 的尺寸 375px * 812px 来写页面，这样一来的话，就可以用下面的 JS 依据当前页面的视口宽度自动计算出根元素 html 的基准 font-size 是多少。
>
> ```js
> (function (doc, win) {
>     var docEl = doc.documentElement,
>         resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
>         psdWidth = 750,  // 设计图宽度
>         recalc = function () {
>             var clientWidth = docEl.clientWidth;
>             if ( !clientWidth ) return;
>             if ( clientWidth >= 640 ) {
>                 docEl.style.fontSize = 200 * ( 640 / psdWidth ) + 'px';
>             } else {
>                 docEl.style.fontSize = 200 * ( clientWidth / psdWidth ) + 'px';
>             }
>         };
> 
>     if ( !doc.addEventListener ) return;
>     // 绑定事件的时候最好配合防抖函数
>     win.addEventListener( resizeEvt, debounce(recalc, 1000), false );
>     doc.addEventListener( 'DOMContentLoaded', recalc, false );
>     
>     function debounce(func, wait) {
>         var timeout;
>         return function () {
>             var context = this;
>             var args = arguments;
>             clearTimeout(timeout)
>             timeout = setTimeout(function(){
>                 func.apply(context, args)
>             }, wait);
>         }
>     }
> })(document, window);
> ```
>
> 比如当视口是 375px 的时候，经过计算 html 的 font-size 会是 100px，这样有什么好处呢？好处就是方便写样式，比如从设计图量出来的 header 高度是 50px 的，那我们写样式的时候就可以直接写：
>
> ```css
> header {
>     height: 0.5rem;
> }
> ```
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9da25c2a6e4541e09bca75e131ecb68a~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> 每个从设计图量出来的尺寸只要除于 100 即可得到当前元素的 rem 值，都不用经过计算，非常方便。偷偷告诉你，如果你把上面那串计算 html 标签 font-size 的 JS 代码中的 200 替换成 2，那在计算 rem 的时候就不需要除于 100 了，从设计图量出多大 px，就直接写多少个 rem。
>
> #### vw/vh
>
> vw 和 vh 分别是相对于屏幕视口宽度和高度而言的长度单位：
>
> - 1vw = 视口宽度均分成 100 份中 1 份的长度；
> - 1vh = 视口高度均分成 100 份中 1 份的长度；
>
> 在 JS 中 100vw = window.innerWidth，100vh = window.innerHeight。
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8448c193d3bf4fb5b396e884dfedb0fa~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> vw/vh 的出现使得多了一种写自适应布局的方案，开发者不再局限于 rem 了。
>
> 相对视口的单位，除了 vw/vh 外，还有 vmin 和 vmax：
>
> - vmin：取 vw 和 vh 中值较小的；
> - vmax：取 vw 和 vh 中值较大的；

#### 24.颜色体系

> CSS 中用于表示颜色的值种类繁多，足够构成一个体系，所以这里就专门拿出一个小节来讲解它。
>
> 根据 [CSS 颜色草案](https://link.juejin.cn?target=https%3A%2F%2Fdrafts.csswg.org%2Fcss-color-3%2F) 中提到的颜色值类型，大概可以把它们分为这几类：
>
> - 颜色关键字
> - transparent 关键字
> - currentColor 关键字
> - RGB 颜色
> - HSL 颜色
>
> #### 颜色关键字
>
> 颜色关键字（color keywords）是不区分大小写的标识符，它表示一个具体的颜色，比如 white（白），黑（black）等；
>
> 可接受的关键字列表在CSS的演变过程中发生了改变：
>
> - CSS 标准 1 只接受 16 个基本颜色，称为 VGA 颜色，因为它们来源于 VGA 显卡所显示的颜色集合而被称为 VGA colors （视频图形阵列色彩）。
> - CSS 标准 2 增加了 orange 关键字。
> - 从一开始，浏览器接受其它的颜色，由于一些早期浏览器是 X11 应用程序，这些颜色大多数是 X11 命名的颜色列表，虽然有一点不同。SVG 1.0 是首个正式定义这些关键字的标准；CSS 色彩标准 3 也正式定义了这些关键字。它们经常被称作扩展的颜色关键字， X11 颜色或 SVG 颜色 。
> - CSS 颜色标准 4 添加可 rebeccapurple 关键字来纪念 web 先锋 Eric Meyer。
>
> 如下这张图是 16 个基础色，又叫 VGA 颜色。截止到目前为止 CSS 颜色关键字总共有 146 个，这里可以查看 [完整的色彩关键字列表](https://link.juejin.cn?target=https%3A%2F%2Fcodepen.io%2Fbulandent%2Fpen%2FgOLovwL)。
>
> ![VGA 颜色](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/09c95f2a03a24f0a929d338efad4b022~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> 需要注意的是如果声明的时候的颜色关键字是错误的，浏览器会忽略它。
>
> #### transparent 关键字
>
> transparent 关键字表示一个完全透明的颜色，即该颜色看上去将是背景色。从技术上说，它是带有 alpha 通道为最小值的黑色，是 rgba(0,0,0,0) 的简写。
>
> 透明关键字有什么应用场景呢？
>
> **实现三角形**
>
> 下面这个图是用 4 条边框填充的正方形，看懂了它你大概就知道该如何用 CSS 写三角形了。
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e553ee7029424667b79e3339c3d4d145~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> ```css
> div {
>     border-top-color: #ffc107;
>     border-right-color: #00bcd4;
>     border-bottom-color: #e26b6b;
>     border-left-color: #cc7cda;
>     border-width: 50px;
>     border-style: solid;
> }
> ```
>
> 用 transparent 实现三角形的原理：
>
> - 首先宽高必须是 0px，通过边框的粗细来填充内容；
> - 那条边需要就要加上颜色，而不需要的边则用 transparent；
> - 想要什么样姿势的三角形，完全由上下左右 4 条边的中有颜色的边和透明的边的位置决定；
> - 等腰三角形：设置一条边有颜色，然后紧挨着的 2 边是透明，且宽度是有颜色边的一半；直角三角形：设置一条边有颜色，然后紧挨着的任何一边透明即可。
>
> 看下示例：
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2b3c505a718540738f45ee741ecaa83f~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> **增大点击区域**
>
> 常常在移动端的时候点击的按钮的区域特别小，但是由于现实效果又不太好把它做大，所以常用的一个手段就是通过透明的边框来增大按钮的点击区域：
>
> ```css
> .btn {
>     border: 5px solid transparent;
> }
> ```
>
> #### currentColor 关键字
>
> currentColor 会取当前元素继承父级元素的文本颜色值或声明的文本颜色值，即 computed 后的 color 值。
>
> 比如，对于如下 CSS，该元素的边框颜色会是 red：
>
> ```css
> .btn {
>     color: red;
>     border: 1px solid currentColor;
> }
> ```
>
> #### RGB[A] 颜色
>
> RGB[A] 颜色是由 R(red)-G(green)-B(blue)-A(alpha) 组成的色彩空间。
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a4f592201b6042d4a0433647babbe62e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> 在 CSS 中，它有两种表示形式：
>
> - 十六进制符号；
> - 函数符；
>
> **十六进制符号**
>
> RGB 中的每种颜色的值范围是 00~ff，值越大表示颜色越深。所以一个颜色正常是 6 个十六进制字符加上 # 组成，比如红色就是 #ff0000。
>
> 如果 RGB 颜色需要加上不透明度，那就需要加上 alpha 通道的值，它的范围也是 00~ff，比如一个带不透明度为 67% 的红色可以这样写 #ff0000aa。
>
> 使用十六进制符号表示颜色的时候，都是用 2 个十六进制表示一个颜色，如果这 2 个字符相同，还可以缩减成只写 1 个，比如，红色 #f00；带 67% 不透明度的红色 #f00a。
>
> **函数符**
>
> 当 RGB 用函数表示的时候，每个值的范围是 0~255 或者 0%~100%，所以红色是 rgb(255, 0, 0)， 或者 rgb(100%, 0, 0)。
>
> 如果需要使用函数来表示带不透明度的颜色值，值的范围是 0~1 及其之间的小数或者 0%~100%，比如带 67% 不透明度的红色是 rgba(255, 0, 0, 0.67) 或者 rgba(100%, 0%, 0%, 67%)
>
> > 需要注意的是 RGB 这 3 个颜色值需要保持一致的写法，要嘛用数字要嘛用百分比，而不透明度的值的可以不用和 RGB 保持一致写法。比如 rgb(100%, 0, 0) 这个写法是无效的；而 rgb(100%, 0%, 0%, 0.67) 是有效的。
>
> 在第 4 代 CSS 颜色标准中，新增了一种新的函数写法，即可以把 RGB 中值的分隔逗号改成空格，而把 RGB 和 alpha 中的逗号改成 /，比如带 67% 不透明度的红色可以这样写 rgba(255 0 0 / 0.67)。另外还把 rgba 的写法合并到 rgb 函数中了，即 rgb 可以直接写带不透明度的颜色。
>
> #### HSL[A] 颜色
>
> HSL[A] 颜色是由色相(hue)-饱和度(saturation)-亮度(lightness)-不透明度组成的颜色体系。
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a416a969aacf40aeb6068ff550623555~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> - 色相（H）是色彩的基本属性，值范围是 0~~360 或者 0deg~~360deg， 0 (或 360) 为红色, 120 为绿色, 240 为蓝色；
> - 饱和度（S）是指色彩的纯度，越高色彩越纯，低则逐渐变灰，取 0~100% 的数值；0% 为灰色， 100% 全色；
> - 亮度（L），取 0~100%，0% 为暗，100% 为白；
> - 不透明度（A），取 0~~100%，或者0~~1及之间的小数；
>
> 写法上可以参考 RGB 的写法，只是参数的值不一样。
>
> 给一个按钮设置不透明度为 67% 的红色的 color 的写法，以下全部写法效果一致：
>
> ```css
> button {
>     color: #ff0000aa;
>     color: #f00a;
>     color: rgba(255, 0, 0, 0.67);
>     color: rgb(100% 0% 0% / 67%);
>     color: hsla(0, 100%, 50%, 67%);
>     color: hsl(0deg 100% 50% / 67%);
> }
> ```
>
> 小提示：在 Chrome DevTools 中可以按住 shift + 鼠标左键可以切换颜色的表示方式。

#### 25.媒体查询

> 媒体查询是指针对不同的设备、特定的设备特征或者参数进行定制化的修改网站的样式。
>
> 你可以通过给 `<link>` 加上 media 属性来指定该样式文件只能对什么设备生效，不指定的话默认是 all，即对所有设备都生效：
>
> ```html
> <link rel="stylesheet" src="styles.css" media="screen" />
> <link rel="stylesheet" src="styles.css" media="print" />
> ```
>
> 都支持哪些设备类型？
>
> - all：适用于所有设备；
> - print：适用于在打印预览模式下在屏幕上查看的分页材料和文档；
> - screen：主要用于屏幕；
> - speech：主要用于语音合成器。
>
> > 需要注意的是：通过 media 指定的  资源尽管不匹配它的设备类型，但是浏览器依然会加载它。
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7036319ab7014a98bf6967c76244e8bc~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> 除了通过 `<link>` 让指定设备生效外，还可以通过 `@media` 让 CSS 规则在特定的条件下才能生效。响应式页面就是使用了 @media 才让一个页面能够同时适配 PC、Pad 和手机端。
>
> ```css
> @media (min-width: 1000px) {}
> ```
>
> 媒体查询支持逻辑操作符：
>
> - and：查询条件都满足的时候才生效；
> - not：查询条件取反；
> - only：整个查询匹配的时候才生效，常用语兼容旧浏览器，使用时候必须指定媒体类型；
> - 逗号或者 or：查询条件满足一项即可匹配；
>
> 媒体查询还支持[众多的媒体特性](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FGuide%2FCSS%2FMedia_queries%23%E5%AA%92%E4%BD%93%E7%89%B9%E6%80%A7)，使得它可以写出很复杂的查询条件：
>
> ```css
> /* 用户设备的最小高度为680px或为纵向模式的屏幕设备 */
> @media (min-height: 680px), screen and (orientation: portrait) {}
> ```

#### 30.使用 CSS 预处理器吗？喜欢哪个？

> 结构清晰，便于扩展。 可以方便地屏蔽浏览器私有语法差异。封装对浏览器语法差异的重复处理， 减少无意义的机械劳动。
>
> 可以轻松实现多重继承。 完全兼容 CSS 代码，可以方便地应用到老项目中。LESS 只是在 CSS 语法上做了扩展，所以老的 CSS 代码也可以与 LESS 代码一同编译。

```
SASS（SASS、LESS没有本质区别，只因为团队前端都是用的SASS）
```

#### 31.CSS 优化、提高性能的方法有哪些？

> **加载性能：**
>
> （1）css压缩：将写好的css进行打包压缩，可以减小文件体积。
>
> （2）css单一样式：当需要下边距和左边距的时候，很多时候会选择使用 margin:top 0 bottom 0；但margin-bottom:bottom;margin-left:left;执行效率会更高。
>
> （3）减少使用@import，建议使用link，因为后者在页面加载时一起加载，前者是等待页面加载完成之后再进行加载。
>
> **选择器性能：**
>
> （1）关键选择器（key selector）。选择器的最后面的部分为关键选择器（即用来匹配目标元素的部分）。CSS选择符是从右到左进行匹配的。当使用后代选择器的时候，浏览器会遍历所有子元素来确定是否是指定的元素等等；
>
> （2）如果规则拥有ID选择器作为其关键选择器，则不要为规则增加标签。过滤掉无关的规则（这样样式系统就不会浪费时间去匹配它们了）。
>
> （3）避免使用通配规则，如*{}计算次数惊人，只对需要用到的元素进行选择。
>
> （4）尽量少的去对标签进行选择，而是用class。
>
> （5）尽量少的去使用后代选择器，降低选择器的权重值。后代选择器的开销是最高的，尽量将选择器的深度降到最低，最高不要超过三层，更多的使用类来关联每一个标签元素。
>
> （6）了解哪些属性是可以通过继承而来的，然后避免对这些属性重复指定规则。
>
> **渲染性能：**
>
> （1）慎重使用高性能属性：浮动、定位。
>
> （2）尽量减少页面重排、重绘。
>
> （3）去除空规则：｛｝。空规则的产生原因一般来说是为了预留样式。去除这些空规则无疑能减少css文档体积。
>
> （4）属性值为0时，不加单位。
>
> （5）属性值为浮动小数0.**，可以省略小数点之前的0。
>
> （6）标准化各种浏览器前缀：带浏览器前缀的在前。标准属性在后。
>
> （7）不使用@import前缀，它会影响css的加载速度。
>
> （8）选择器优化嵌套，尽量避免层级过深。
>
> （9）css雪碧图，同一页面相近部分的小图标，方便使用，减少页面的请求次数，但是同时图片本身会变大，使用时，优劣考虑清楚，再使用。
>
> （10）正确使用display的属性，由于display的作用，某些样式组合会无效，徒增样式体积的同时也影响解析性能。
>
> （11）不滥用web字体。对于中文网站来说WebFonts可能很陌生，国外却很流行。web fonts通常体积庞大，而且一些浏览器在下载web fonts时会阻塞页面渲染损伤性能。
>
> **可维护性、健壮性：**
>
> （1）将具有相同属性的样式抽离出来，整合并通过class在页面中进行使用，提高css的可维护性。
>
> （2）样式与内容分离：将css代码定义到外部css中。

详细资料可以参考：
[《CSS 优化、提高性能的方法有哪些？》](https://www.zhihu.com/question/19886806)
[《CSS 优化，提高性能的方法》](https://www.jianshu.com/p/4e673bf24a3b)

#### 32.浏览器是怎样解析 CSS 选择器的？

```
样式系统从关键选择器开始匹配，然后左移查找规则选择器的祖先元素。只要选择器的子树一直在工作，样式系统就会持续左移，直
到和规则匹配，或者是因为不匹配而放弃该规则。

试想一下，如果采用从左至右的方式读取CSS规则，那么大多数规则读到最后（最右）才会发现是不匹配的，这样做会费时耗能，
最后有很多都是无用的；而如果采取从右向左的方式，那么只要发现最右边选择器不匹配，就可以直接舍弃了，避免了许多无效匹配。
```

详细资料可以参考：
[《探究 CSS 解析原理》](https://juejin.im/entry/5a123c55f265da432240cc90)

#### 33.在网页中应该使用奇数还是偶数的字体？为什么呢？

```
（1）偶数字号相对更容易和web设计的其他部分构成比例关系。比如：当我用了14px的正文字号，我可能会在一些地方用14
×0.5=7px的margin，在另一些地方用14×1.5=21px的标题字号。
（2）浏览器缘故，低版本的浏览器ie6会把奇数字体强制转化为偶数，即13px渲染为14px。
（3）系统差别，早期的Windows里，中易宋体点阵只有12和14、15、16px，唯独缺少13px。
```

详细资料可以参考：
[《谈谈网页中使用奇数字体和偶数字体》](https://blog.csdn.net/jian_xi/article/details/79346477)
[《现在网页设计中的为什么少有人用 11px、13px、15px 等奇数的字体？》](https://www.zhihu.com/question/20440679)

#### 34.margin 和 padding 分别适合什么场景使用？

> - 需要在border外侧添加空白，且空白处不需要背景（色）时，使用 margin；
> - 需要在border内测添加空白，且空白处需要背景（色）时，使用 padding。

```
margin是用来隔开元素与元素的间距；padding是用来隔开元素与内容的间隔。
margin用于布局分开元素使元素与元素互不相干。
padding用于元素与内容之间的间隔，让内容（文字）与（包裹）元素之间有一段距离。

何时应当使用margin：
•需要在border外侧添加空白时。
•空白处不需要背景（色）时。
•上下相连的两个盒子之间的空白，需要相互抵消时。如15px+20px的margin，将得到20px的空白。

何时应当时用padding：
•需要在border内测添加空白时。
•空白处需要背景（色）时。
•上下相连的两个盒子之间的空白，希望等于两者之和时。如15px+20px的padding，将得到35px的空白。
```

#### 35.抽离样式模块怎么写，说出思路，有无实践经验？[阿里航旅的面试题]

```
我的理解是把常用的css样式单独做成css文件……通用的和业务相关的分离出来，通用的做成样式模块儿共享，业务相关的，放
进业务相关的库里面做成对应功能的模块儿。
```

详细资料可以参考：
[《CSS 规范-分类方法》](http://nec.netease.com/standard/css-sort.html)

#### 36.简单说一下 css3 的 all 属性。

```
all属性实际上是所有CSS属性的缩写，表示，所有的CSS属性都怎样怎样，但是，不包括unicode-bidi和direction
这两个CSS属性。支持三个CSS通用属性值，initial,inherit,unset。

initial是初始值的意思，也就是该元素元素都除了unicode-bidi和direction以外的CSS属性都使用属性的默认初始
值。

inherit是继承的意思，也就是该元素除了unicode-bidi和direction以外的CSS属性都继承父元素的属性值。

unset是取消设置的意思，也就是当前元素浏览器或用户设置的CSS忽略，然后如果是具有继承特性的CSS，如color，则
使用继承值；如果是没有继承特性的CSS属性，如background-color，则使用初始值。

```

详细资料可以参考：
[《简单了解 CSS3 的 all 属性》](https://www.zhangxinxu.com/wordpress/2016/03/know-about-css3-all/)

#### 37.为什么不建议使用统配符初始化 css 样式。

```
采用*{padding:0;margin:0;}这样的写法好处是写起来很简单，但是是通配符，需要把所有的标签都遍历一遍，当网站较大时，
样式比较多，这样写就大大的加强了网站运行的负载，会使网站加载的时候需要很长一段时间，因此一般大型的网站都有分层次的一
套初始化样式。

出于性能的考虑，并不是所有标签都会有padding和margin，因此对常见的具有默认padding和margin的元素初始化即
可，并不需使用通配符*来初始化。
```

#### 38.absolute 的 containingblock（包含块）计算方式跟正常流有什么不同？

```
（1）内联元素也可以作为“包含块”所在的元素；

（2）“包含块”所在的元素不是父块级元素，而是最近的position不为static的祖先元素或根元素；

（3）边界是padding box而不是content box。
```

#### 39.对于 hasLayout 的理解？

```
hasLayout是IE特有的一个属性。很多的IE下的css bug都与其息息相关。在IE中，一个元素要么自己对自身的内容进
行计算大小和组织，要么依赖于父元素来计算尺寸和组织内容。当一个元素的hasLayout属性值为true时，它负责对自己和可
能的子孙元素进行尺寸计算和定位。虽然这意味着这个元素需要花更多的代价来维护自身和里面的内容，而不是依赖于祖先元素来完
成这些工作。
```

详细资料可以参考：
[《CSS 基础篇--CSS 中 IE 浏览器的 hasLayout，IE 低版本的 bug 根源》](https://segmentfault.com/a/1190000010883974)
[《CSS 魔法堂：hasLayout 原来是这样的！》](https://segmentfault.com/a/1190000004632071)

#### 40.元素竖向的百分比设定是相对于容器的高度吗？

```
如果是height的话，是相对于包含块的高度。

如果是padding或者margin竖直方向的属性则是相对于包含块的宽度。
```

#### 41.全屏滚动的原理是什么？用到了 CSS 的哪些属性？（待深入实践）

```
原理：有点类似于轮播，整体的元素一直排列下去，假设有5个需要展示的全屏页面，那么高度是500%，只是展示100%，容器及容
器内的页面取当前可视区高度，同时容器的父级元素overflow属性值设为hidden，通过更改容器可视区的位置来实现全
屏滚动效果。主要是响应鼠标事件，页面通过CSS的动画效果，进行移动。

overflow：hidden；transition：all 1000 ms ease；
```

详细资料可以参考：
[《js 实现网页全屏切换（平滑过渡），鼠标滚动切换》](https://blog.csdn.net/liona_koukou/article/details/52680409)
[《用 ES6 写全屏滚动插件》](https://juejin.im/post/5aeef41cf265da0ba0630de0)

#### 43.视差滚动效果，如何给每页做不同的动画？（回到顶部，向下滑动要再次出现，和只出现一次分别怎么做？）

```
视差滚动是指多层背景以不同的速度移动，形成立体的运动效果，带来非常出色的视觉体验。
```

详细资料可以参考：
[《如何实现视差滚动效果的网页？》](https://www.zhihu.com/question/20990029)

#### 44.如何修改 chrome 记住密码后自动填充表单的黄色背景？

```
chrome表单自动填充后，input文本框的背景会变成黄色的，通过审查元素可以看到这是由于chrome会默认给自动填充的in
put表单加上input:-webkit-autofill私有属性，然后对其赋予以下样式：

{
background-color:rgb(250,255,189)!important;
background-image:none!important;
color:rgb(0,0,0)!important;
}

对chrome默认定义的background-color，background-image，color使用important是不能提高其优先级的，但是
其他属性可使用。

使用足够大的纯色内阴影来覆盖input输入框的黄色背景，处理如下

input:-webkit-autofill,textarea:-webkit-autofill,select:-webkit-autofill{
-webkit-box-shadow:000px 1000px white inset;
border:1px solid #CCC !important;
}

```

详细资料可以参考：
[《去掉 chrome 记住密码后的默认填充样式》](https://blog.csdn.net/zsl_955200/article/details/78276209)
[《修改谷歌浏览器 chrome 记住密码后自动填充表单的黄色背景》](https://blog.csdn.net/M_agician/article/details/73381706)

#### 45.怎么让 Chrome 支持小于 12px 的文字？

```
在谷歌下css设置字体大小为12px及以下时，显示都是一样大小，都是默认12px。

解决办法：

（1）可以使用Webkit的内核的-webkit-text-size-adjust的私有CSS属性来解决，只要加了-webkit-text-size
-adjust:none;字体大小就不受限制了。但是chrome更新到27版本之后就不可以用了。所以高版本chrome谷歌浏览器
已经不再支持-webkit-text-size-adjust样式，所以要使用时候慎用。

（2）还可以使用css3的transform缩放属性-webkit-transform:scale(0.5);注意-webkit-transform:scale(0.
75);收缩的是整个元素的大小，这时候，如果是内联元素，必须要将内联元素转换成块元素，可以使用display：block/
inline-block/...；

（3）使用图片：如果是内容固定不变情况下，使用将小于12px文字内容切出做图片，这样不影响兼容也不影响美观。
```

详细资料可以参考：
[《谷歌浏览器不支持 CSS 设置小于 12px 的文字怎么办？》](https://570109268.iteye.com/blog/2406562)

#### 46.让页面里的字体变清晰，变细用 CSS 怎么做？

```
webkit内核的私有属性：-webkit-font-smoothing，用于字体抗锯齿，使用后字体看起来会更清晰舒服。

在MacOS测试环境下面设置-webkit-font-smoothing:antialiased;但是这个属性仅仅是面向MacOS，其他操作系统设
置后无效。
```

详细资料可以参考：
[《让字体变的更清晰 CSS 中-webkit-font-smoothing》](https://blog.csdn.net/huo_bao/article/details/50251585)

#### 47.font-style 属性中 italic 和 oblique 的区别？

```
italic和oblique这两个关键字都表示“斜体”的意思。

它们的区别在于，italic是使用当前字体的斜体字体，而oblique只是单纯地让文字倾斜。如果当前字体没有对应的斜体字体，
则退而求其次，解析为oblique，也就是单纯形状倾斜。
```

#### 48.设备像素、css 像素、设备独立像素、dpr、ppi 之间的区别？

```
设备像素指的是物理像素，一般手机的分辨率指的就是设备像素，一个设备的设备像素是不可变的。

css像素和设备独立像素是等价的，不管在何种分辨率的设备上，css像素的大小应该是一致的，css像素是一个相对单位，它是相
对于设备像素的，一个css像素的大小取决于页面缩放程度和dpr的大小。

dpr指的是设备像素和设备独立像素的比值，一般的pc屏幕，dpr=1。在iphone4时，苹果推出了retina屏幕，它的dpr
为2。屏幕的缩放会改变dpr的值。

ppi指的是每英寸的物理像素的密度，ppi越大，屏幕的分辨率越大。
```

详细资料可以参考：
[《什么是物理像素、虚拟像素、逻辑像素、设备像素，什么又是 PPI,DPI,DPR 和 DIP》](https://www.cnblogs.com/libin-1/p/7148377.html)
[《前端工程师需要明白的「像素」》](https://www.jianshu.com/p/af6dad66e49a)
[《CSS 像素、物理像素、逻辑像素、设备像素比、PPI、Viewport》](https://github.com/jawil/blog/issues/21)
[《前端开发中像素的概念》](https://github.com/wujunchuan/wujunchuan.github.io/issues/15)

#### 49.layout viewport、visual viewport 和 ideal viewport 的区别？

相关知识点：

```
如果把移动设备上浏览器的可视区域设为viewport的话，某些网站就会因为viewport太窄而显示错乱，所以这些浏览器就决定
默认情况下把viewport设为一个较宽的值，比如980px，这样的话即使是那些为桌面设计的网站也能在移动浏览器上正常显示了。
ppk把这个浏览器默认的viewport叫做layout viewport。

layout viewport的宽度是大于浏览器可视区域的宽度的，所以我们还需要一个viewport来代表浏览器可视区域的大小，ppk把
这个viewport叫做visual viewport。

ideal viewport是最适合移动设备的viewport，ideal viewport的宽度等于移动设备的屏幕宽度，只要在css中把某一元
素的宽度设为ideal viewport的宽度（单位用px），那么这个元素的宽度就是设备屏幕的宽度了，也就是宽度为100%的效果。i
deal viewport的意义在于，无论在何种分辨率的屏幕下，那些针对ideal viewport而设计的网站，不需要用户手动缩放，也
不需要出现横向滚动条，都可以完美的呈现给用户。
```

回答：

```
移动端一共需要理解三个viewport的概念的理解。

第一个视口是布局视口，在移动端显示网页时，由于移动端的屏幕尺寸比较小，如果网页使用移动端的屏幕尺寸进行布局的话，那么整
个页面的布局都会显示错乱。所以移动端浏览器提供了一个layout viewport布局视口的概念，使用这个视口来对页面进行布局展
示，一般layout viewport的大小为980px，因此页面布局不会有太大的变化，我们可以通过拖动和缩放来查看到这个页面。

第二个视口指的是视觉视口，visual viewport指的是移动设备上我们可见的区域的视口大小，一般为屏幕的分辨率的大小。visu
al viewport和layout viewport的关系，就像是我们通过窗户看外面的风景，视觉视口就是窗户，而外面的风景就是布局视口
中的网页内容。

第三个视口是理想视口，由于layout viewport一般比visual viewport要大，所以想要看到整个页面必须通过拖动和缩放才
能实现。所以又提出了ideal viewport的概念，ideal viewport下用户不用缩放和滚动条就能够查看到整个页面，并且页面在
不同分辨率下显示的内容大小相同。ideal viewport其实就是通过修改layout viewport的大小，让它等于设备的宽度，这个
宽度可以理解为是设备独立像素，因此根据ideal viewport设计的页面，在不同分辨率的屏幕下，显示应该相同。
```

详细资料可以参考：
[《移动前端开发之 viewport 的深入理解》](https://www.cnblogs.com/2050/p/3877280.html)
[《说说移动前端中 viewport（视口）》](https://www.html.cn/archives/5975)
[《移动端适配知识你到底知多少》](https://juejin.im/post/5b6d21daf265da0f9d1a2ed7#heading-14)

#### 51.如果需要手动写动画，你认为最小时间间隔是多久，为什么？（阿里）

```
多数显示器默认频率是60Hz，即1秒刷新60次，所以理论上最小间隔为1/60*1000ms＝16.7ms
```

#### 52.如何让去除 inline-block 元素间间距？

```
移除空格、使用margin负值、使用font-size:0、letter-spacing、word-spacing
```

详细资料可以参考：
[《去除 inline-block 元素间间距的 N 种方法》](https://www.zhangxinxu.com/wordpress/2012/04/inline-block-space-remove-%E5%8E%BB%E9%99%A4%E9%97%B4%E8%B7%9D/)

#### 53.overflow:scroll 时不能平滑滚动的问题怎么处理？

```
以下代码可解决这种卡顿的问题：-webkit-overflow-scrolling:touch;是因为这行代码启用了硬件加速特性，所以滑动很流
畅。
```

详细资料可以参考：
[《解决页面使用 overflow:scroll 在 iOS 上滑动卡顿的问题》](https://www.jianshu.com/p/1f4693d0ad2d)

#### 54.有一个高度自适应的 div，里面有两个 div，一个高度 100px，希望另一个填满剩下的高度。

```
（1）外层div使用position：relative；高度要求自适应的div使用position:absolute;top:100px;bottom:0;
left:0;right:0;

（2）使用flex布局，设置主轴为竖轴，第二个div的flex-grow为1。
```

详细资料可以参考：
[《有一个高度自适应的 div，里面有两个 div，一个高度 100px，希望另一个填满剩下的高度(三种方案)》](https://blog.csdn.net/xutongbao/article/details/79408522)

#### 55.png、jpg、gif 这些图片格式解释一下，分别什么时候用。有没有了解过 webp？ 常见的图片格式及使用场景？

相关知识点：

```
（1）BMP，是无损的、既支持索引色也支持直接色的、点阵图。这种图片格式几乎没有对数据进行压缩，所以BMP格式的图片通常
具有较大的文件大小。

（2）GIF是无损的、采用索引色的、点阵图。采用LZW压缩算法进行编码。文件小，是GIF格式的优点，同时，GIF格式还具
有支持动画以及透明的优点。但，GIF格式仅支持8bit的索引色，所以GIF格式适用于对色彩要求不高同时需要文件体积
较小的场景。

（3）JPEG是有损的、采用直接色的、点阵图。JPEG的图片的优点，是采用了直接色，得益于更丰富的色彩，JPEG非常适合用来
存储照片，与GIF相比，JPEG不适合用来存储企业Logo、线框类的图。因为有损压缩会导致图片模糊，而直接色的选用，
又会导致图片文件较GIF更大。

（4）PNG-8是无损的、使用索引色的、点阵图。PNG是一种比较新的图片格式，PNG-8是非常好的GIF格式替代者，在可能的
情况下，应该尽可能的使用PNG-8而不是GIF，因为在相同的图片效果下，PNG-8具有更小的文件体积。除此之外，PNG-8
还支持透明度的调节，而GIF并不支持。现在，除非需要动画的支持，否则我们没有理由使用GIF而不是PNG-8。

（5）PNG-24是无损的、使用直接色的、点阵图。PNG-24的优点在于，它压缩了图片的数据，使得同样效果的图片，PNG-24格
式的文件大小要比BMP小得多。当然，PNG24的图片还是要比JPEG、GIF、PNG-8大得多。

（6）SVG是无损的、矢量图。SVG是矢量图。这意味着SVG图片由直线和曲线以及绘制它们的方法组成。当你放大一个SVG图
片的时候，你看到的还是线和曲线，而不会出现像素点。这意味着SVG图片在放大时，不会失真，所以它非常适合用来绘制企
业Logo、Icon等。

（7）WebP是谷歌开发的一种新图片格式，WebP是同时支持有损和无损压缩的、使用直接色的、点阵图。从名字就可以看出来它是
为Web而生的，什么叫为Web而生呢？就是说相同质量的图片，WebP具有更小的文件体积。现在网站上充满了大量的图片，
如果能够降低每一个图片的文件大小，那么将大大减少浏览器和服务器之间的数据传输量，进而降低访问延迟，提升访问体验。

•在无损压缩的情况下，相同质量的WebP图片，文件大小要比PNG小26%；
•在有损压缩的情况下，具有相同图片精度的WebP图片，文件大小要比JPEG小25%~34%；
•WebP图片格式支持图片透明度，一个无损压缩的WebP图片，如果要支持透明度只需要22%的格外文件大小。

但是目前只有Chrome浏览器和Opera浏览器支持WebP格式，兼容性不太好。
```

回答：

```
我了解到的一共有七种常见的图片的格式。

（1）第一种是BMP格式，它是无损压缩的，支持索引色和直接色的点阵图。由于它基本上没有进行压缩，因此它的文件体积一般比
较大。

（2）第二种是GIF格式，它是无损压缩的使用索引色的点阵图。由于使用了LZW压缩方法，因此文件的体积很小。并且GIF还
支持动画和透明度。但因为它使用的是索引色，所以它适用于一些对颜色要求不高且需要文件体积小的场景。

（3）第三种是JPEG格式，它是有损压缩的使用直接色的点阵图。由于使用了直接色，色彩较为丰富，一般适用于来存储照片。但
由于使用的是直接色，可能文件的体积相对于GIF格式来说更大。

（4）第四种是PNG-8格式，它是无损压缩的使用索引色的点阵图。它是GIF的一种很好的替代格式，它也支持透明度的调整，并
且文件的体积相对于GIF格式更小。一般来说如果不是需要动画的情况，我们都可以使用PNG-8格式代替GIF格式。

（5）第五种是PNG-24格式，它是无损压缩的使用直接色的点阵图。PNG-24的优点是它使用了压缩算法，所以它的体积比BMP
格式的文件要小得多，但是相对于其他的几种格式，还是要大一些。

（6）第六种格式是svg格式，它是矢量图，它记录的图片的绘制方式，因此对矢量图进行放大和缩小不会产生锯齿和失真。它一般
适合于用来制作一些网站logo或者图标之类的图片。

（7）第七种格式是webp格式，它是支持有损和无损两种压缩方式的使用直接色的点阵图。使用webp格式的最大的优点是，在相
同质量的文件下，它拥有更小的文件体积。因此它非常适合于网络图片的传输，因为图片体积的减少，意味着请求时间的减小，
这样会提高用户的体验。这是谷歌开发的一种新的图片格式，目前在兼容性上还不是太好。
```

详细资料可以参考：
[《图片格式那么多，哪种更适合你？》](https://www.cnblogs.com/xinzhao/p/5130410.html)

#### 56.浏览器如何判断是否支持 webp 格式图片

```
（1）宽高判断法。通过创建image对象，将其src属性设置为webp格式的图片，然后在onload事件中获取图片的宽高，如
果能够获取，则说明浏览器支持webp格式图片。如果不能获取或者触发了onerror函数，那么就说明浏览器不支持webp格
式的图片。

（2）canvas判断方法。我们可以动态的创建一个canvas对象，通过canvas的toDataURL将设置为webp格式，然后判断
返回值中是否含有image/webp字段，如果包含则说明支持WebP，反之则不支持。
```

详细资料可以参考：
[《判断浏览器是否支持 WebP 图片》](https://blog.csdn.net/jesslu/article/details/82495061)
[《toDataURL()》](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLCanvasElement/toDataURL)

#### 57.什么是 Cookie 隔离？（或者说：请求资源的时候不要让它带 cookie 怎么做）

```
网站向服务器请求的时候，会自动带上cookie这样增加表头信息量，使请求变慢。

如果静态文件都放在主域名下，那静态文件请求的时候都带有的cookie的数据提交给server的，非常浪费流量，所以不如隔离开
，静态资源放CDN。

因为cookie有域的限制，因此不能跨域提交请求，故使用非主要域名的时候，请求头中就不会带有cookie数据，这样可以降低请
求头的大小，降低请求时间，从而达到降低整体请求延时的目的。

同时这种方式不会将cookie传入WebServer，也减少了WebServer对cookie的处理分析环节，提高了webserver的
http请求的解析速度。
```

详细资料可以参考：
[《CDN 是什么？使用 CDN 有什么优势？》](https://www.zhihu.com/question/36514327?rf=37353035)

#### 58.style 标签写在 body 后与 body 前有什么区别？

```
页面加载自上而下当然是先加载样式。写在body标签后由于浏览器以逐行方式对HTML文档进行解析，当解析到写在尾部的样式
表（外联或写在style标签）会导致浏览器停止之前的渲染，等待加载且解析样式表完成之后重新渲染，在windows的IE下可
能会出现FOUC现象（即样式失效导致的页面闪烁问题）
```

#### 59.什么是 CSS 预处理器/后处理器？

> CSS预处理器定义了一种新的语言，其基本思想是，用一种专门的编程语言，为CSS增加了一些编程的特性，将CSS作为目标生成
> 文件，然后开发者就只要使用这种语言进行编码工作。通俗的说，CSS预处理器用一种专门的编程语言，进行Web页面样式设计，然
> 后再编译成正常的CSS文件
>
> **预处理器，** 如：`less`，`sass`，`stylus`，用来预编译`sass`或者`less`，增加了`css`代码的复用性。层级，`mixin`， 变量，循环， 函数等对编写以及开发UI组件都极为方便。
>
> CSS后处理器是对CSS进行处理，并最终生成CSS的预处理器，它属于广义上的CSS预处理器。我们很久以前就在用CSS后
> 处理器了，最典型的例子是CSS压缩工具（如clean-css），只不过以前没单独拿出来说过。还有最近比较火的Autoprefixer，
> 以CanIUse上的浏览器支持数据为基础，自动处理兼容性问题
>
> **后处理器，** 如： `postCss`，通常是在完成的样式表中根据`css`规范处理`css`，让其更加有效。目前最常做的是给`css`属性添加浏览器私有前缀，实现跨浏览器兼容性的问题。
>
> `css`预处理器为`css`增加一些编程特性，无需考虑浏览器的兼容问题，可以在`CSS`中使用变量，简单的逻辑程序，函数等在编程语言中的一些基本的性能，可以让`css`更加的简洁，增加适应性以及可读性，可维护性等。
>
> 其它`css`预处理器语言：`Sass（Scss）`, `Less`, `Stylus`, `Turbine`, `Swithch css`, `CSS Cacheer`, `DT Css`。
>
> 使用原因：
>
> - 结构清晰， 便于扩展
> - 可以很方便的屏蔽浏览器私有语法的差异
> - 可以轻松实现多重继承
> - 完美的兼容了`CSS`代码，可以应用到老项目中

详细资料可以参考：
[《CSS 预处理器和后处理器》](https://blog.csdn.net/yushuangyushuang/article/details/79209752)

#### 60.阐述一下 CSSSprites

> CSSSprites（精灵图），将一个页面涉及到的所有图片都包含到一张大图中去，然后利用CSS的 background-image，background-repeat，background-position属性的组合进行背景定位。
>
> **优点：**
>
> - 利用`CSS Sprites`能很好地减少网页的http请求，从而大大提高了页面的性能，这是`CSS Sprites`最大的优点；
> - `CSS Sprites`能减少图片的字节，把3张图片合并成1张图片的字节总是小于这3张图片的字节总和。
>
> **缺点：**
>
> - 在图片合并时，要把多张图片有序的、合理的合并成一张图片，还要留好足够的空间，防止板块内出现不必要的背景。在宽屏及高分辨率下的自适应页面，如果背景不够宽，很容易出现背景断裂；
> - `CSSSprites`在开发的时候相对来说有点麻烦，需要借助`photoshop`或其他工具来对每个背景单元测量其准确的位置。
> - 维护方面：`CSS Sprites`在维护的时候比较麻烦，页面背景有少许改动时，就要改这张合并的图片，无需改的地方尽量不要动，这样避免改动更多的`CSS`，如果在原来的地方放不下，又只能（最好）往下加图片，这样图片的字节就增加了，还要改动`CSS`。

#### 61.transition 和 animation 的区别

> **transition是过度属性**，强调过度，它的实现需要触发一个事件（比如鼠标移动上去，焦点，点击等）才执行动画。它类似于flash的补间动画，设置一个开始关键帧，一个结束关键帧。
>
> **animation是动画属性**，它的实现不需要触发事件，设定好时间之后可以自己执行，且可以循环一个动画。它也类似于flash的补间动画，但是它可以设置多个关键帧（用@keyframe定义）完成动画。

```
transition关注的是CSS property的变化，property值和时间的关系是一个三次贝塞尔曲线。

animation作用于元素本身而不是样式属性，可以使用关键帧的概念，应该说可以实现更自由的动画效果。
```

详细资料可以参考：
[《CSSanimation 与 CSStransition 有何区别？》](https://www.zhihu.com/question/19749045)
[《CSS3Transition 和 Animation 区别及比较》](https://blog.csdn.net/cddcj/article/details/53582334)
[《CSS 动画简介》](http://www.ruanyifeng.com/blog/2014/02/css_transition_and_animation.html)
[《CSS 动画：animation、transition、transform、translate》](https://juejin.im/post/5b137e6e51882513ac201dfb)

#### 62.什么是首选最小宽度？

```
“首选最小宽度”，指的是元素最适合的最小宽度。

东亚文字（如中文）最小宽度为每个汉字的宽度。

西方文字最小宽度由特定的连续的英文字符单元决定。并不是所有的英文字符都会组成连续单元，一般会终止于空格（普通空格）、短
横线、问号以及其他非英文字符等。

如果想让英文字符和中文一样，每一个字符都用最小宽度单元，可以试试使用CSS中的word-break:break-all。
```

#### 69.为什么 height:100%会无效？

```
对于普通文档流中的元素，百分比高度值要想起作用，其父级必须有一个可以生效的高度值。

原因是如果包含块的高度没有显式指定（即高度由内容决定），并且该元素不是绝对定位，则计算值为auto，因为解释成了auto，
所以无法参与计算。

使用绝对定位的元素会有计算值，即使祖先元素的height计算为auto也是如此。
```

#### 70.min-width/max-width 和 min-height/max-height 属性间的覆盖规则？

```
（1）max-width会覆盖width，即使width是行类样式或者设置了!important。

（2）min-width会覆盖max-width，此规则发生在min-width和max-width冲突的时候。

(3)发生冲突的情况下，max-height会覆盖掉 height, min-height又会覆盖掉max-height

(4)同理，发生冲突的情况下，max-width会覆盖掉 width, min-width又会覆盖掉max-width
```

#### 71.内联盒模型基本概念

```
（1）内容区域（content area）。内容区域指一种围绕文字看不见的盒子，其大小仅受字符本身特性控制，本质上是一个字符盒子
（character box）；但是有些元素，如图片这样的替换元素，其内容显然不是文字，不存在字符盒子之类的，因此，对于这些
元素，内容区域可以看成元素自身。

（2）内联盒子（inline box）。“内联盒子”不会让内容成块显示，而是排成一行，这里的“内联盒子”实际指的就是元素的“外在盒
子”，用来决定元素是内联还是块级。该盒子又可以细分为“内联盒子”和“匿名内联盒子”两类。

（3）行框盒子（line box），每一行就是一个“行框盒子”（实线框标注），每个“行框盒子”又是由一个一个“内联盒子”组成的。

（4）包含块（containing box），由一行一行的“行框盒子”组成。
```

#### 72.什么是幽灵空白节点？

```
“幽灵空白节点”是内联盒模型中非常重要的一个概念，具体指的是：在HTML5文档声明中，内联元素的所有解析和渲染表现就如同
每个行框盒子的前面有一个“空白节点”一样。这个“空白节点”永远透明，不占据任何宽度，看不见也无法通过脚本获取，就好像幽灵
一样，但又确确实实地存在，表现如同文本节点一样，因此，我称之为“幽灵空白节点”。
```

#### 73.什么是替换元素？

```
通过修改某个属性值呈现的内容就可以被替换的元素就称为“替换元素”。因此，<img>、<object>、<video>、<iframe>或者表
单元素<textarea>和<input>和<select>都是典型的替换元素。

替换元素除了内容可替换这一特性以外，还有以下一些特性。

（1）内容的外观不受页面上的CSS的影响。用专业的话讲就是在样式表现在CSS作用域之外。如何更改替换元素本身的外观需要
类似appearance属性，或者浏览器自身暴露的一些样式接口，

（2）有自己的尺寸。在Web中，很多替换元素在没有明确尺寸设定的情况下，其默认的尺寸（不包括边框）是300像素×150像
素，如<video>、<iframe>或者<canvas>等，也有少部分替换元素为0像素，如<img>图片，而表单元素的替换元素
的尺寸则和浏览器有关，没有明显的规律。

（3）在很多CSS属性上有自己的一套表现规则。比较具有代表性的就是vertical-align属性，对于替换元素和非替换元素，ve
rtical-align属性值的解释是不一样的。比方说vertical-align的默认值的baseline，很简单的属性值，基线之意，
被定义为字符x的下边缘，而替换元素的基线却被硬生生定义成了元素的下边缘。

（4）所有的替换元素都是内联水平元素，也就是替换元素和替换元素、替换元素和文字都是可以在一行显示的。但是，替换元素默认
的display值却是不一样的，有的是inline，有的是inline-block。
```

#### 74.替换元素的计算规则？

```
替换元素的尺寸从内而外分为3类：固有尺寸、HTML尺寸和CSS尺寸。

（1）固有尺寸指的是替换内容原本的尺寸。例如，图片、视频作为一个独立文件存在的时候，都是有着自己的宽度和高度的。

（2）HTML尺寸只能通过HTML原生属性改变，这些HTML原生属性包括<img>的width和height属性、<input>的s
ize属性、<textarea>的cols和rows属性等。

（3）CSS尺寸特指可以通过CSS的width和height或者max-width/min-width和max-height/min-height设置的
尺寸，对应盒尺寸中的content box。

这3层结构的计算规则具体如下

（1）如果没有CSS尺寸和HTML尺寸，则使用固有尺寸作为最终的宽高。

（2）如果没有CSS尺寸，则使用HTML尺寸作为最终的宽高。

（3）如果有CSS尺寸，则最终尺寸由CSS属性决定。

（4）如果“固有尺寸”含有固有的宽高比例，同时仅设置了宽度或仅设置了高度，则元素依然按照固有的宽高比例显示。

（5）如果上面的条件都不符合，则最终宽度表现为300像素，高度为150像素。

（6）内联替换元素和块级替换元素使用上面同一套尺寸计算规则。
```

#### 75.content 与替换元素的关系？

```
content属性生成的对象称为“匿名替换元素”。

（1）我们使用content生成的文本是无法选中、无法复制的，好像设置了user select:none声明一般，但是普通元素的文本
却可以被轻松选中。同时，content生成的文本无法被屏幕阅读设备读取，也无法被搜索引擎抓取，因此，千万不要自以为是
地把重要的文本信息使用content属性生成，因为这对可访问性和SEO都很不友好。

（2）content生成的内容不能左右:empty伪类。

（3）content动态生成值无法获取。
```

#### 76.margin:auto 的填充规则？

```
margin的'auto'可不是摆设，是具有强烈的计算意味的关键字，用来计算元素对应方向应该获得的剩余间距大小。但是触发mar
gin:auto计算有一个前提条件，就是width或height为auto时，元素是具有对应方向的自动填充特性的。

（1）如果一侧定值，一侧auto，则auto为剩余空间大小。
（2）如果两侧均是auto，则平分剩余空间。
```

#### 77.margin 无效的情形

```
（1）display计算值inline的非替换元素的垂直margin是无效的。对于内联替换元素，垂直margin有效，并且没有ma
rgin合并的问题。

（2）表格中的<tr>和<td>元素或者设置display计算值是table-cell或table-row的元素的margin都是无效的。

（3）绝对定位元素非定位方位的margin值“无效”。

（4）定高容器的子元素的margin-bottom或者宽度定死的子元素的margin-right的定位“失效”。
```

#### 78.border 的特殊性？

```
（1）border-width却不支持百分比。

（2）border-style的默认值是none，有一部分人可能会误以为是solid。这也是单纯设置border-width或border-col
or没有边框显示的原因。

（3）border-style:double的表现规则：双线宽度永远相等，中间间隔±1。

（4）border-color默认颜色就是color色值。

（5）默认background背景图片是相对于padding box定位的。
```

#### 79.什么是基线和 x-height？

```
字母x的下边缘（线）就是我们的基线。

x-height指的就是小写字母x的高度，术语描述就是基线和等分线（meanline）（也称作中线，midline）之间的距离。在C
SS世界中，middle指的是基线往上1/2x-height高度。我们可以近似理解为字母x交叉点那个位置。

ex是CSS中的一个相对单位，指的是小写字母x的高度，没错，就是指x-height。ex的价值就在其副业上不受字体和字号影
响的内联元素的垂直居中对齐效果。内联元素默认是基线对齐的，而基线就是x的底部，而1ex就是一个x的高度。
```

#### 80.line-height 的特殊性？对**line-height 的理解及其赋值方式**

> **（1）line-height的概念：**
>
> - line-height 指一行文本的高度，包含了字间距，实际上是下一行基线到上一行基线距离；
> - 如果一个标签没有定义 height 属性，那么其最终表现的高度由 line-height 决定；
> - 一个容器没有设置高度，那么撑开容器高度的是 line-height，而不是容器内的文本内容；
> - 把 line-height 值设置为 height 一样大小的值可以实现单行文字的垂直居中；
> - line-height 和 height 都能撑开一个高度；
>
> **（2）line-height 的赋值方式：**
>
> - 带单位：px 是固定值，而 em 会参考父元素 font-size 值计算自身的行高
> - 纯数字：会把比例传递给后代。例如，父级行高为 1.5，子元素字体为 18px，则子元素行高为 1.5 * 18 = 27px
> - 百分比：将计算后的值传递给后代

```
（1）对于非替换元素的纯内联元素，其可视高度完全由line-height决定。对于文本这样的纯内联元素，line-height就是高
度计算的基石，用专业说法就是指定了用来计算行框盒子高度的基础高度。

（2）内联元素的高度由固定高度和不固定高度组成，这个不固定的部分就是这里的“行距”。换句话说，line-height之所以起作
用，就是通过改变“行距”来实现的。在CSS中，“行距”分散在当前文字的上方和下方，也就是即使是第一行文字，其上方也是
有“行距”的，只不过这个“行距”的高度仅仅是完整“行距”高度的一半，因此，也被称为“半行距”。

（3）行距=line-height-font-size。

（4）border以及line-height等传统CSS属性并没有小数像素的概念。如果标注的是文字上边距，则向下取整；如果是文字下
边距，则向上取整。

（5）对于纯文本元素，line-height直接决定了最终的高度。但是，如果同时有替换元素，则line-height只能决定最小高度。

（6）对于块级元素，line-height对其本身是没有任何作用的，我们平时改变line-height，块级元素的高度跟着变化实际上是
通过改变块级元素里面内联级别元素占据的高度实现的。

（7）line-height的默认值是normal，还支持数值、百分比值以及长度值。为数值类型时，其最终的计算值是和当前font-si
ze相乘后的值。为百分比值时，其最终的计算值是和当前font-size相乘后的值。为长度值时原意不变。

（8）如果使用数值作为line-height的属性值，那么所有的子元素继承的都是这个值；但是，如果使用百分比值或者长度值作为
属性值，那么所有的子元素继承的是最终的计算值。

（9）无论内联元素line-height如何设置，最终父级元素的高度都是由数值大的那个line-height决定的。

（10）只要有“内联盒子”在，就一定会有“行框盒子”，就是每一行内联元素外面包裹的一层看不见的盒子。然后，重点来了，在每个
“行框盒子”前面有一个宽度为0的具有该元素的字体和行高属性的看不见的“幽灵空白节点”。
```

#### 81.vertical-align 的特殊性？

```
（1）vertical-align的默认值是baseline，即基线对齐，而基线的定义是字母x的下边缘。因此，内联元素默认都是沿着字
母x的下边缘对齐的。对于图片等替换元素，往往使用元素本身的下边缘作为基线。：一个inline-block元素，如果里面
没有内联元素，或者overflow不是visible，则该元素的基线就是其margin底边缘；否则其基线就是元素里面最后一行
内联元素的基线。

（2）vertical-align:top就是垂直上边缘对齐，如果是内联元素，则和这一行位置最高的内联元素的顶部对齐；如果display
计算值是table-cell的元素，我们不妨脑补成<td>元素，则和<tr>元素上边缘对齐。

（3）vertical-align:middle是中间对齐，对于内联元素，元素的垂直中心点和行框盒子基线往上1/2x-height处对齐。对
于table-cell元素，单元格填充盒子相对于外面的表格行居中对齐。

（4）vertical-align支持数值属性，根据数值的不同，相对于基线往上或往下偏移，如果是负值，往下偏移，如果是正值，往上
偏移。

（5）vertical-align属性的百分比值则是相对于line-height的计算值计算的。

（6）vertical-align起作用是有前提条件的，这个前提条件就是：只能应用于内联元素以及display值为table-cell的元
素。

（7）table-cell元素设置vertical-align垂直对齐的是子元素，但是其作用的并不是子元素，而是table-cell元素自身。
```

#### 82.overflow 的特殊性？

```
（1）一个设置了overflow:hidden声明的元素，假设同时存在border属性和padding属性，则当子元素内容超出容器宽度
高度限制的时候，剪裁的边界是border box的内边缘，而非padding box的内边缘。

（2）HTML中有两个标签是默认可以产生滚动条的，一个是根元素<html>，另一个是文本域<textarea>。

（3）滚动条会占用容器的可用宽度或高度。

（4）元素设置了overflow:hidden声明，里面内容高度溢出的时候，滚动依然存在，仅仅滚动条不存在！
```

#### 83.无依赖绝对定位是什么？

```
没有设置left/top/right/bottom属性值的绝对定位称为“无依赖绝对定位”。

无依赖绝对定位其定位的位置和没有设置position:absolute时候的位置相关。
```

#### 84.absolute 与 overflow 的关系？

```
（1）如果overflow不是定位元素，同时绝对定位元素和overflow容器之间也没有定位元素，则overflow无法对absolute
元素进行剪裁。

（2）如果overflow的属性值不是hidden而是auto或者scroll，即使绝对定位元素高宽比overflow元素高宽还要大，也
都不会出现滚动条。

（3）overflow元素自身transform的时候，Chrome和Opera浏览器下的overflow剪裁是无效的。
```

#### 85.clip 裁剪是什么？

```
所谓“可访问性隐藏”，指的是虽然内容肉眼看不见，但是其他辅助设备却能够进行识别和访问的隐藏。

clip剪裁被我称为“最佳可访问性隐藏”的另外一个原因就是，它具有更强的普遍适应性，任何元素、任何场景都可以无障碍使用。
```

#### 86.relative 的特殊性？

```
（1）相对定位元素的left/top/right/bottom的百分比值是相对于包含块计算的，而不是自身。注意，虽然定位位移是相对自身，但是百分比值的计算值不是。

（2）top和bottom这两个垂直方向的百分比值计算跟height的百分比值是一样的，都是相对高度计算的。同时，如果包含块的高度是auto，那么计算值是0，偏移无效，也就是说，如果父元素没有设定高度或者不是“格式化高度”，那么relative类似top:20%的代码等同于top:0。

（3）当相对定位元素同时应用对立方向定位值的时候，也就是top/bottom和left/right同时使用的时候，只有一个方向的定位属性会起作用。而谁起作用则是与文档流的顺序有关的，默认的文档流是自上而下、从左往右，因此top/bottom同时使用的时候，bottom失效；left/right同时使用的时候，right失效。
```

#### 91.font-weight 的特殊性？

```
如果使用数值作为font-weight属性值，必须是100～900的整百数。因为这里的数值仅仅是外表长得像数值，实际上是一个具有特定含义的关键字，并且这里的数值关键字和字母关键字之间是有对应关系的。
```

#### 92.text-indent 的特殊性？

```
（1）text-indent仅对第一行内联盒子内容有效。

（2）非替换元素以外的display计算值为inline的内联元素设置text-indent值无效，如果计算值inline-block/inli
ne-table则会生效。

（3）<input>标签按钮text-indent值无效。

（4）<button>标签按钮text-indent值有效。

（5）text-indent的百分比值是相对于当前元素的“包含块”计算的，而不是当前元素。
```

#### 93.letter-spacing 与字符间距？

```
letter-spacing可以用来控制字符之间的间距，这里说的“字符”包括英文字母、汉字以及空格等。

letter-spacing具有以下一些特性。

（1）继承性。
（2）默认值是normal而不是0。虽然说正常情况下，normal的计算值就是0，但两者还是有差别的，在有些场景下，letter-spacing会调整normal的计算值以实现更好的版面布局。
（3）支持负值，且值足够大的时候，会让字符形成重叠，甚至反向排列。
（4）和text-indent属性一样，无论值多大或多小，第一行一定会保留至少一个字符。
（5）支持小数值，即使0.1px也是支持的。
（6）暂不支持百分比值。
```

#### 94.word-spacing 与单词间距？

```
letter-spacing作用于所有字符，但word-spacing仅作用于空格字符。换句话说，word-spacing的作用就是增加空格的间隙
宽度。
```

#### 95.white-space 与换行和空格的控制？

```
white-space属性声明了如何处理元素内的空白字符，这类空白字符包括Space（空格）键、Enter（回车）键、Tab（制表符）
键产生的空白。因此，white-space可以决定图文内容是否在一行显示（回车空格是否生效），是否显示大段连续空白（空格是否
生效）等。

其属性值包括下面这些。
•normal：合并空白字符和换行符。
•pre：空白字符不合并，并且内容只在有换行符的地方换行。
•nowrap：该值和normal一样会合并空白字符，但不允许文本环绕。
•pre-wrap：空白字符不合并，并且内容只在有换行符的地方换行，同时允许文本环绕。
•pre-line：合并空白字符，但只在有换行符的地方换行，允许文本环绕。
```

#### 96.隐藏元素的 background-image 到底加不加载？

相关知识点：

```
根据测试，一个元素如果display计算值为none，在IE浏览器下（IE8～IE11，更高版本不确定）依然会发送图片请求，Fire
fox浏览器不会，至于Chrome和Safari浏览器则似乎更加智能一点：如果隐藏元素同时又设置了background-image，则图片
依然会去加载；如果是父元素的display计算值为none，则背景图不会请求，此时浏览器或许放心地认为这个背景图暂时是不会使
用的。

如果不是background-image，而是<img>元素，则设置display:none在所有浏览器下依旧都会请求图片资源。

还需要注意的是如果设置的样式没有对应的元素，则background-image也不会加载。hover情况下的background-image，在触
发时加载。
```

回答：

-（1）元素的背景图片

-元素本身设置 display:none，会请求图片 -父级元素设置 display:none，不会请求图片 -样式没有元素使用，不会请求
-:hover 样式下，触发时请求

-（2）img 标签图片任何情况下都会请求图片

详细资料可以参考：
[《CSS 控制前端图片 HTTP 请求的各种情况示例》](https://www.jb51.net/css/469033.html)

#### 97.如何实现单行／多行文本溢出的省略？

```css
/*单行文本溢出*/
p {
    overflow: hidden;            // 溢出隐藏
    text-overflow: ellipsis;      // 溢出用省略号显示
    white-space: nowrap;         // 规定段落中的文本不进行换行
}

/*多行文本溢出*/
p {
  position: relative;
  line-height: 1.5em;
  /*高度为需要显示的行数*行高，比如这里我们显示两行，则为3*/
  height: 3em;
  overflow: hidden;           // 溢出隐藏
}

p:after {
  content: '...';
  position: absolute;
  bottom: 0;
  right: 0;
  background-color: #fff;
}
```

详细资料可以参考：
[《【CSS/JS】如何实现单行／多行文本溢出的省略》](https://zhuanlan.zhihu.com/p/30707916)
[《CSS 多行文本溢出省略显示》](https://juejin.im/entry/587f453e1b69e60058555a5f)

#### 98.常见的元素隐藏方式？

-（1）使用 display:none;隐藏元素，渲染树不会包含该渲染对象，因此该元素不会在页面中占据位置，也不会响应绑定的监听事件。

-（2）使用 visibility:hidden;隐藏元素。元素在页面中仍占据空间，但是不会响应绑定的监听事件。

-（3）使用 opacity:0;将元素的透明度设置为 0，以此来实现元素的隐藏。元素在页面中仍然占据空间，并且能够响应元素绑定的监听事件。

-（4）通过使用绝对定位将元素移除可视区域内，以此来实现元素的隐藏。

-（5）通过 z-index 负值，来使其他元素遮盖住该元素，以此来实现隐藏。

-（6）通过 clip/clip-path 元素裁剪的方法来实现元素的隐藏，这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。

-（7）通过 transform:scale(0,0)来将元素缩放为 0，以此来实现元素的隐藏。这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。

> ####  display:none与visibility:hidden的区别
>
> 这两个属性都是让元素隐藏，不可见。**两者区别如下：**
>
> （1）**在渲染树中**
>
> - `display:none`会让元素完全从渲染树中消失，渲染时不会占据任何空间；
> - `visibility:hidden`不会让元素从渲染树中消失，渲染的元素还会占据相应的空间，只是内容不可见。
>
> （2）**是否是继承属性**
>
> - `display:none`是非继承属性，子孙节点会随着父节点从渲染树消失，通过修改子孙节点的属性也无法显示；
> - `visibility:hidden`是继承属性，子孙节点消失是由于继承了`hidden`，通过设置`visibility:visible`可以让子孙节点显示；
>
> （3）修改常规文档流中元素的 `display` 通常会造成文档的重排，但是修改`visibility`属性只会造成本元素的重绘；
>
> （4）如果使用读屏器，设置为`display:none`的内容不会被读取，设置为`visibility:hidden`的内容会被读取。

详细资料可以参考：
[《CSS 隐藏元素的八种方法》](https://juejin.im/post/584b645a128fe10058a0d625#heading-2)

#### 105.你知道 CSS 中不同属性设置为百分比%时对应的计算基准？

```
公式：当前元素某CSS属性值 = 基准 * 对应的百分比
元素的 position 为 relative 和 absolute 时，top和bottom、left和right基准分别为包含块的 height、width
元素的 position 为 fixed 时，top和bottom、left和right基准分别为初始包含块（也就是视口）的 height、width，移动设备较为复杂，基准为 Layout viewport 的 height、width
元素的 height 和 width 设置为百分比时，基准分别为包含块的 height 和 width
元素的 margin 和 padding 设置为百分比时，基准为包含块的 width（易错）
元素的 border-width，不支持百分比
元素的 text-indent，基准为包含块的 width

元素的 border-radius，基准为分别为自身的height、width
元素的 background-size，基准为分别为自身的height、width
元素的 translateX、translateY，基准为分别为自身的height、width
元素的 line-height，基准为自身的 font-size

元素的 font-size，基准为父元素字体
```

#### 106. link和@import的区别

> 两者都是外部引用CSS的方式，它们的区别如下：
>
> - link是XHTML标签，除了加载CSS外，还可以定义RSS等其他事务；@import属于CSS范畴，只能加载CSS。
> - link引用CSS时，在页面载入时同时加载；@import需要页面网页完全载入以后加载。
> - link是XHTML标签，无兼容问题；@import是在CSS2.1提出的，低版本的浏览器不支持。
> - link支持使用Javascript控制DOM去改变样式；而@import不支持。

#### 107.为什么有时候⽤**translate**来改变位置⽽不是定位？

> translate 是 transform 属性的⼀个值。改变transform或opacity不会触发浏览器重新布局（reflow）或重绘（repaint），只会触发复合（compositions）。⽽改变绝对定位会触发重新布局，进⽽触发重绘和复合。transform使浏览器为元素创建⼀个 GPU 图层，但改变绝对定位会使⽤到 CPU。 因此translate()更⾼效，可以缩短平滑动画的绘制时间。 ⽽translate改变位置时，元素依然会占据其原始空间，绝对定位就不会发⽣这种情况。

### 二.页面布局

#### 1.几种常见的 CSS 布局

> #### 1.Flex 布局
>
> 布局的传统解决方案，基于盒状模型，依赖 display 属性 + position 属性 + float 属性。它对于那些特殊布局非常不方便，比如，垂直居中就不容易实现。
>
> Flex 是 Flexible Box 的缩写，意为"弹性布局",用来为盒状模型提供最大的灵活性。指定容器 display: flex 即可。 简单的分为容器属性和元素属性。
>
> 容器的属性：
>
> - flex-direction：决定主轴的方向（即子 item 的排列方法）flex-direction: row | row-reverse | column | column-reverse;
> - flex-wrap：决定换行规则 flex-wrap: nowrap | wrap | wrap-reverse;
> - flex-flow： .box { flex-flow: || ; }
> - justify-content：对其方式，水平主轴对齐方式
> - align-items：对齐方式，竖直轴线方向
> - align-content
>
> 项目的属性（元素的属性）：
>
> - order 属性：定义项目的排列顺序，顺序越小，排列越靠前，默认为 0
> - flex-grow 属性：定义项目的放大比例，即使存在空间，也不会放大
> - flex-shrink 属性：定义了项目的缩小比例，当空间不足的情况下会等比例的缩小，如果 定义个 item 的 flow-shrink 为 0，则为不缩小
> - flex-basis 属性：定义了在分配多余的空间，项目占据的空间。
> - flex：是 flex-grow 和 flex-shrink、flex-basis 的简写，默认值为 0 1 auto。
> - align-self：允许单个项目与其他项目不一样的对齐方式，可以覆盖
> - align-items，默认属 性为 auto，表示继承父元素的 align-items 比如说，用 flex 实现圣杯布局
>
> #### 2.Rem 布局
>
> 首先 Rem 相对于根(html)的 font-size 大小来计算。简单的说它就是一个相对单例 如:font-size:10px;,那么（1rem = 10px）了解计算原理后首先解决怎么在不同设备上设置 html 的 font-size 大小。其实 rem 布局的本质是等比缩放，一般是基于宽度。
>
> **优点**：可以快速适用移动端布局，字体，图片高度
>
> **缺点**：
>
> ①目前 ie 不支持，对 pc 页面来讲使用次数不多；
>  ②数据量大：所有的图片，盒子都需要我们去给一个准确的值；才能保证不同机型的适配；
>  ③在响应式布局中，必须通过 js 来动态控制根元素 font-size 的大小。也就是说 css 样式和 js 代码有一定的耦合性。且必须将改变 font-size 的代码放在 css 样式之前。
>
> #### 3.百分比布局
>
> 通过百分比单位 " % " 来实现响应式的效果。通过百分比单位可以使得浏览器中的组件的宽和高随着浏览器的变化而变化，从而实现响应式的效果。 直观的理解，我们可能会认为子元素的百分比完全相对于直接父元素，height 百分比相 对于 height，width 百分比相对于 width。 padding、border、margin 等等不论是垂直方向还是水平方向，都相对于直接父元素的 width。 除了 border-radius 外，还有比如 translate、background-size 等都是相对于自身的。
>
> **缺点**：
>
> （1）计算困难
>  （2）各个属性中如果使用百分比，相对父元素的属性并不是唯一的。造成我们使用百分比单位容易使布局问题变得复杂。
>
> #### 4.浮动布局
>
> 浮动布局:当元素浮动以后可以向左或向右移动，直到它的外边缘碰到包含它的框或者另外一个浮动元素的边框为止。元素浮动以后会脱离正常的文档流，所以文档的普通流中的框就变的好像浮动元素不存在一样。
>
> **优点**
>
> 这样做的优点就是在图文混排的时候可以很好的使文字环绕在图片周围。另外当元素浮动了起来之后，它有着块级元素的一些性质例如可以设置宽高等，但它与inline-block还是有一些区别的，第一个就是关于横向排序的时候，float可以设置方向而inline-block方向是固定的；还有一个就是inline-block在使用时有时会有空白间隙的问题
>
> **缺点**
>
> 最明显的缺点就是浮动元素一旦脱离了文档流，就无法撑起父元素，`会造成父级元素高度塌陷`。

> 常用的布局单位包括像素（`px`），百分比（`%`），`em`，`rem`，`vw/vh`。
>
> **（1）像素**（`px`）是页面布局的基础，一个像素表示终端（电脑、手机、平板等）屏幕所能显示的最小的区域，像素分为两种类型：CSS像素和物理像素：
>
> - **CSS像素**：为web开发者提供，在CSS中使用的一个抽象单位；
> - **物理像素**：只与设备的硬件密度有关，任何设备的物理像素都是固定的。
>
> **（2）百分比**（`%`），当浏览器的宽度或者高度发生变化时，通过百分比单位可以使得浏览器中的组件的宽和高随着浏览器的变化而变化，从而实现响应式的效果。一般认为子元素的百分比相对于直接父元素。
>
> **（3）em和rem**相对于px更具灵活性，它们都是相对长度单位，它们之间的区别：**em相对于父元素，rem相对于根元素。**
>
> - **em：** 文本相对长度单位。相对于当前对象内文本的字体尺寸。如果当前行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸(默认16px)。(相对父元素的字体大小倍数)。
> - **rem：** rem是CSS3新增的一个相对单位，相对于根元素（html元素）的font-size的倍数。**作用**：利用rem可以实现简单的响应式布局，可以利用html元素中字体的大小与屏幕间的比值来设置font-size的值，以此实现当屏幕分辨率变化时让元素也随之变化。
>
> **（4）vw/vh**是与视图窗口有关的单位，vw表示相对于视图窗口的宽度，vh表示相对于视图窗口高度，除了vw和vh外，还有vmin和vmax两个相关的单位。
>
> - vw：相对于视窗的宽度，视窗宽度是100vw；
> - vh：相对于视窗的高度，视窗高度是100vh；
> - vmin：vw和vh中的较小值；
> - vmax：vw和vh中的较大值；
>
> **vw/vh** 和百分比很类似，两者的区别：
>
> - 百分比（`%`）：大部分相对于祖先元素，也有相对于自身的情况比如（border-radius、translate等)
> - vw/vm：相对于视窗的尺寸

详细的资料可以参考：
[《几种常见的 CSS 布局》](https://juejin.im/post/5bbcd7ff5188255c80668028#heading-12)

#### 2.一个满屏品字布局如何设计?

```
简单的方式：
	上面的div宽100%，
	下面的两个div分别宽50%，
	然后用float或者inline使其不换行即可
```

#### 3.如何居中 div？

> 让元素在父元素中呈现出水平垂直居中的形态，无非就 2 种情况：
>
> - 单行的文本、inline 或者 inline-block 元素；
> - 固定宽高的块级盒子；
> - 不固定宽高的块级盒子；

-水平居中：给 div 设置一个宽度，然后添加 margin:0 auto 属性

```css
div {
  width: 200px;
  margin: 0 auto;
}
```

-水平居中，利用 text-align:center 实现

```css
.container {
  background: rgba(0, 0, 0, 0.5);
  text-align: center;
  font-size: 0;
}

.box {
  display: inline-block;
  width: 500px;
  height: 400px;
  background-color: pink;
}
```

-让绝对定位的 div 居中

```css
div {
  position: absolute;
  width: 300px;
  height: 300px;
  margin: auto;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  background-color: pink; /*方便看效果*/
}
```

>  水平垂直居中的实现
>
>  - 利用绝对定位，先将元素的左上角通过top:50%和left:50%定位到页面的中心，然后再通过translate来调整元素的中心点到页面的中心。该方法需要**考虑浏览器兼容问题。**
>
>  ```css
>  .parent {    
>     position: relative;
>  } 
>  .child {    
>     position: absolute;    
>     left: 50%;    
>     top: 50%;    
>     transform: translate(-50%,-50%);
>  }
>  ```
>
>  - 利用绝对定位，设置四个方向的值都为0，并将margin设置为auto，由于宽高固定，因此对应方向实现平分，可以实现水平和垂直方向上的居中。该方法适用于**盒子有宽高**的情况：
>
>    > 固定宽高的块级盒子
>
>  ```css
>  .parent {
>     position: relative;
>  }
>  
>  .child {
>     position: absolute;
>     top: 0;
>     bottom: 0;
>     left: 0;
>     right: 0;
>     margin: auto;
>  }
>  ```
>
>  - 利用绝对定位，先将元素的左上角通过top:50%和left:50%定位到页面的中心，然后再通过margin负值来调整元素的中心点到页面的中心。该方法适用于**盒子宽高已知**的情况
>
>    > 固定宽高的块级盒子
>
>  ```css
>  .parent {
>     position: relative;
>  }
>  
>  .child {
>     position: absolute;
>     top: 50%;
>     left: 50%;
>     margin-top: -50px;     /* 自身 height 的一半 */
>     margin-left: -50px;    /* 自身 width 的一半 */
>  }
>  ```
>
>  - 使用flex布局，通过align-items:center和justify-content:center设置容器的垂直和水平方向上为居中对齐，然后它的子元素也可以实现垂直和水平的居中。该方法要**考虑兼容的问题**，该方法在移动端用的较多：
>
>  ```css
>  .parent {
>     display: flex;
>     justify-content:center;
>     align-items:center;
>  }
>  ```

#### 4.使用 rem 布局的优缺点？

```
优点：
在屏幕分辨率千差万别的时代，只要将rem与屏幕分辨率关联起来就可以实现页面的整体缩放，使得在设备上的展现都统一起来了。
而且现在浏览器基本都已经支持rem了，兼容性也非常的好。

缺点：
（1）在奇葩的dpr设备上表现效果不太好，比如一些华为的高端机型用rem布局会出现错乱。
（2）使用iframe引用也会出现问题。
（3）rem在多屏幕尺寸适配上与当前两大平台的设计哲学不一致。即大屏的出现到底是为了看得又大又清楚，还是为了看的更多的问
题。
```

详细资料可以参考：
[《css3 的字体大小单位 rem 到底好在哪？》](https://www.zhihu.com/question/21504656)
[《VW:是时候放弃 REM 布局了》](https://www.jianshu.com/p/e8ae1c3861dc)
[《为什么设计稿是 750px》](https://blog.csdn.net/Honeymao/article/details/76795089)
[《使用 Flexible 实现手淘 H5 页面的终端适配》](https://github.com/amfe/article/issues/17)

详细资料可以参考：

#### 5.css 实现上下固定中间自适应布局？

```css
利用绝对定位实现body {
  padding: 0;
  margin: 0;
}

.header {
  position: absolute;
  top: 0;
  width: 100%;
  height: 100px;
  background: red;
}

.container {
  position: absolute;
  top: 100px;
  bottom: 100px;
  width: 100%;
  background: green;
}

.footer {
  position: absolute;
  bottom: 0;
  height: 100px;
  width: 100%;
  background: red;
}

利用flex布局实现html,
body {
  height: 100%;
}

body {
  display: flex;
  padding: 0;
  margin: 0;
  flex-direction: column;
}

.header {
  height: 100px;
  background: red;
}

.container {
  flex-grow: 1;
  background: green;
}

.footer {
  height: 100px;
  background: red;
}
```

详细资料可以参考：
[《css 实现上下固定中间自适应布局》](https://www.jianshu.com/p/30bc9751e3e8)

#### 6.css 两栏布局的实现？

> 现在有以下 DOM 结构：
>
> ```html
> <div class="outer">
>   <div class="left">左侧</div>
>   <div class="right">右侧</div>
> </div>
> ```
> 
>1. 利用浮动，左边元素宽度固定 ，设置向左浮动。将右边元素的 `margin-left` 设为固定宽度 。注意，因为右边元素的 `width` 默认为 `auto` ，所以会自动撑满父元素。
> 
>```css
> .outer {
>   height: 100px;
>   overflow: hidden;
> }
> .left {
>   float: left;
>   width: 200px;
>   /*height: 100%;
>   background: lightcoral;*/
> }
> .right {
>   margin-left: 200px;
>   /*height: 100%;
>   background: lightseagreen;*/
> }
> ```
>
> 2. 同样利用浮动，左边元素宽度固定 ，设置向左浮动。右侧元素设置 `overflow: hidden;` 这样右边就触发了 `BFC` ，`BFC` 的区域不会与浮动元素发生重叠，所以两侧就不会发生重叠。
>
> ```css
> .outer {
>   height: 100px;
> }
> .left {
>   float: left;
>   width: 200px;
>   /* height: 100%;
>   background: lightcoral;*/
> }
> .right {
>   overflow: hidden;
>   /*height: 100%;
>   background: lightseagreen;*/
> }
> ```
>
> 3. 利用 `flex` 布局，左边元素固定宽度，右边的元素设置 `flex: 1` 。
>
> ```css
> .outer {
>   display: flex;
>   /*height: 100px;*/
> }
> .left {
>   width: 200px;
>   /*height: 100%;
>   background: lightcoral;*/
> }
> .right {
>   flex: 1;
>   /*height: 100%;
>   background: lightseagreen;*/
> }
> ```
>
> 4. 利用绝对定位，父级元素设为相对定位。左边元素 `absolute` 定位，宽度固定。右边元素的 `margin-left` 的值设为左边元素的宽度值。
>
> ```css
> .outer {
>   position: relative;
>   /*height: 100px;*/
> }
> .left {
>   position: absolute;
>   width: 200px;
>   /*height: 100%;
>   background: lightcoral;*/
> }
> .right {
>   margin-left: 200px;
>   /*height: 100%;
>   background: lightseagreen;*/
> }
> ```
>
> 5. 利用绝对定位，父级元素设为相对定位。左边元素宽度固定，右边元素 `absolute` 定位， `left` 为宽度大小，其余方向定位为 `0` 。
>
> ```css
> .outer {
>   position: relative;
>   height: 100px;
> }
> .left {
>   width: 200px;
>   /*height: 100%;
>   background: lightcoral;*/
> }
> .right {
>   position: absolute;
>   left: 200px;
>   top: 0;
>   right: 0;
>   bottom: 0;
>   /*height: 100%;
>   background: lightseagreen;*/
> }
> ```

[《两栏布局 demo 展示》](http://cavszhouyou.top/Demo-Display/TwoColumnLayout/index.html)

回答：

两栏布局一般指的是页面中一共两栏，左边固定，右边自适应的布局，一共有四种实现的方式。

以左边宽度固定为 200px 为例

-（1）利用浮动，将左边元素宽度设置为 200px，并且设置向左浮动。将右边元素的 margin-left 设置为 200px，宽度设置为 auto（默认为 auto，撑满整个父元素）。

-（2）第二种是利用 flex 布局，将左边元素的放大和缩小比例设置为 0，基础大小设置为 200px。将右边的元素的放大比例设置为 1，缩小比例设置为 1，基础大小设置为 auto。

-（3）第三种是利用绝对定位布局的方式，将父级元素设置相对定位。左边元素设置为 absolute 定位，并且宽度设置为 200px。将右边元素的 margin-left 的值设置为 200px。

-（4）第四种还是利用绝对定位的方式，将父级元素设置为相对定位。左边元素宽度设置为 200px，右边元素设置为绝对定位，左边定位为 200px，其余方向定位为 0。

#### 7.css 三栏布局的实现？

> 三栏布局一般指的是页面中一共有三栏，**左右两栏宽度固定，中间自适应的布局**，三栏布局的具体实现：
>
> - 利用**绝对定位**，左右两栏设置为绝对定位，中间设置对应方向大小的margin的值。
>
> ```css
> .outer {
>   position: relative;
>   height: 100px;
> }
> 
> .left {
>   position: absolute;
>   width: 100px;
>   /*height: 100px;
>   background: tomato;*/
> }
> 
> .right {
>   position: absolute;
>   top: 0;
>   right: 0;
>   width: 200px;
>   /*height: 100px;
>   background: gold;*/
> }
> 
> .center {
>   margin-left: 100px;
>   margin-right: 200px;
>   /*height: 100px;
>   background: lightgreen;*/
> }
> ```
>
> - 利用flex布局，左右两栏设置固定大小，中间一栏设置为flex:1。
>
> ```css
> .outer {
>   display: flex;
>   height: 100px;
> }
> 
> .left {
>   width: 100px;
>   background: tomato;
> }
> 
> .right {
>   width: 100px;
>   background: gold;
> }
> 
> .center {
>   flex: 1;
>   background: lightgreen;
> }
> ```
>
> - 利用浮动，左右两栏设置固定大小，并设置对应方向的浮动。中间一栏设置左右两个方向的margin值，注意这种方式**，中间一栏必须放到最后：**
>
> ```css
> .outer {
>   height: 100px;
>   overflow:hidden;
> }
> 
> .left {
>   float: left;
>   width: 100px;
>   /*height: 100px;
>   background: tomato;*/
> }
> 
> .right {
>   float: right;
>   width: 200px;
>   /*height: 100px;
>   background: gold;*/
> }
> 
> .center {
>   margin-left: 100px;
>   margin-right: 200px;
>   /*height: 100px;
>   background: lightgreen;*/
> }
> ```
>
> 圣杯布局和双飞翼布局的目的：
>
> - 三栏布局，中间一栏最先加载和渲染（**内容最重要，这就是为什么还需要了解这种布局的原因**）。
> - 两侧内容固定，中间内容随着宽度自适应。
> - 一般用于 PC 网页。
>
> 圣杯布局和双飞翼布局的技术总结：
>
> - 使用 `float` 布局。
> - 两侧使用 `margin` 负值，以便和中间内容横向重叠。
> - 防止中间内容被两侧覆盖，圣杯布局用 `padding` ，双飞翼布局用 `margin` 。
>
> **圣杯布局：** HTML 结构：
>
> ```html
> <div class="outer">
>   <p class="center">我是中间</p>
>   <p class="left">我是左边</p>
>   <p class="right">我是右边</p>
> </div>
> ```
>
> CSS 样式：
>
> > 圣杯布局，利用浮动和负边距来实现。父级元素设置左右的 padding，三列均设置向左浮动，中间一列放在最前面，宽度设置为父级元素的宽度，因此后面两列都被挤到了下一行，通过设置 margin 负值将其移动到上一行，再利用相对定位，定位到两边。
>
> ```css
> .outer {
>   height: 100px;
>   padding-left: 100px;
>   padding-right: 200px;
> }
> .center {
>   float: left;
>   width: 100%;
>   /*height: 100px;
>   background: lightgreen;*/
> }
> 
> .left {
>   float: left;/*浮动，保证之后的"margin-left"属性可以将自身拉到上一行*/
>   width: 100px;/*固定宽度*/
>   margin-left: -100%;/*该属性可以将元素向左移动属性值的单位，100%相对于父容器计算*/
>   position: relative;
>   left: -100px;
>   /*height: 100px;
>   background: tomato;*/
> }
> 
> .right {
>   float: right;
>   width: 200px;
>   margin-left: -200px;
>   position: relative;
>   left: 200px;
>   /*height: 100px;
>   background: gold;*/
> }
> ```
>
> **双飞翼布局：** HTML 结构：
>
> ```html
> <div class="outer">
>   <p class="center">
>       <div class="wrapper"> </div>
>   </p>
>   <p class="left">我是左边</p>
>   <p class="right">我是右边</p>
> </div>
> ```
>
> CSS 样式：
>
> > 双飞翼布局，双飞翼布局相对于圣杯布局来说，左右位置的保留是通过中间列的 margin 值来实现的，而不是通过父元素的 padding 来实现的。本质上来说，也是通过浮动和外边距负值来实现的。
>
> ```css
> .outer {
>   height: 100px;
> }
> 
> .center {
>   margin-left: 100px;
>   margin-right: 200px;
>   height: 100px;
> }
> 
> .left {
>   float: left;
>   margin-left: -100%;
>   width: 100px;
>     
>   /*height: 100px;
>   background: tomato;*/
> }
> 
> .right {
>   float: left;
>   margin-left: -200px;
>   width: 200px;
>     
>   /*height: 100px;
>   background: gold;*/
> }
> 
> .wrapper {
>   float: left;
>   width: 100%;
>   /*height: 100px;
>   background: lightgreen;*/
> }
> ```
>
> tips：上述代码中 `margin-left: -100%` 相对的是父元素的 `content` 宽度，即不包含 `paddig` 、 `border` 的宽度。
>
> 其实以上问题需要掌握 **margin 负值问题** 即可很好理解。

[《三栏布局 demo 展示》](http://cavszhouyou.top/Demo-Display/ThreeColumnLayout/index.html)

回答：

```
三栏布局一般指的是页面中一共有三栏，左右两栏宽度固定，中间自适应的布局，一共有五种实现方式。

这里以左边宽度固定为100px，右边宽度固定为200px为例。

（1）利用绝对定位的方式，左右两栏设置为绝对定位，中间设置对应方向大小的margin的值。

（2）利用flex布局的方式，左右两栏的放大和缩小比例都设置为0，基础大小设置为固定的大小，中间一栏设置为auto。

（3）利用浮动的方式，左右两栏设置固定大小，并设置对应方向的浮动。中间一栏设置左右两个方向的margin值，注意这种方式，中间一栏必须放到最后。

（4）圣杯布局，利用浮动和负边距来实现。父级元素设置左右的padding，三列均设置向左浮动，中间一列放在最前面，宽度设置为父级元素的宽度，因此后面两列都被挤到了下一行，通过设置margin负值将其移动到上一行，再利用相对定位，定位到两边。圣杯布局中间列的宽度不能小于两边任意列的宽度，而双飞翼布局则不存在这个问题。

（5）双飞翼布局，双飞翼布局相对于圣杯布局来说，左右位置的保留是通过中间列的margin值来实现的，而不是通过父元素的padding来实现的。本质上来说，也是通过浮动和外边距负值来实现的。
```

#### 8.px、em、rem的区别及使用场景

> **三者的区别：**
>
> - px是固定的像素，一旦设置了就无法因为适应页面大小而改变。
> - em和rem相对于px更具有灵活性，他们是相对长度单位，其长度不是固定的，更适用于响应式布局。
> - em是相对于其父元素来设置字体大小，这样就会存在一个问题，进行任何元素设置，都有可能需要知道他父元素的大小。而rem是相对于根元素，这样就意味着，只需要在根元素确定一个参考值。
>
> **使用场景：**
>
> - 对于只需要适配少部分移动设备，且分辨率对页面影响不大的，使用px即可 。
> - 对于需要适配各种移动设备，使用rem，例如需要适配iPhone和iPad等分辨率差别比较挺大的设备。

#### 9.flex布局

> Flex是FlexibleBox的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。任何一个容器都可以指定为Flex布局。行内元素也可以使用Flex布局。注意，设为Flex布局以后，**子元素的float、clear和vertical-align属性将失效**。采用Flex布局的元素，称为Flex容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为Flex项目（flex item），简称"项目"。容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis），项目默认沿水平主轴排列。
>
> 以下6个属性设置在**容器上**：
>
> - flex-direction属性决定主轴的方向（即项目的排列方向）。
> - flex-wrap属性定义，如果一条轴线排不下，如何换行。
> - flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。
> - justify-content属性定义了项目在主轴上的对齐方式。
> - align-items属性定义项目在交叉轴上如何对齐。
> - align-content属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。
>
> 以下6个属性设置在**项目上**：
>
> - order属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
> - flex-grow属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
> - flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
> - flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
> - flex属性是flex-grow，flex-shrink和flex-basis的简写，默认值为0 1 auto。
> - align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。
>
> **简单来说：** flex布局是CSS3新增的一种布局方式，可以通过将一个元素的display属性值设置为flex从而使它成为一个flex容器，它的所有子元素都会成为它的项目。一个容器默认有两条轴：一个是水平的主轴，一个是与主轴垂直的交叉轴。可以使用flex-direction来指定主轴的方向。可以使用justify-content来指定元素在主轴上的排列方式，使用align-items来指定元素在交叉轴上的排列方式。还可以使用flex-wrap来规定当一行排列不下时的换行方式。对于容器中的项目，可以使用order属性来指定项目的排列顺序，还可以使用flex-grow来指定当排列空间有剩余的时候，项目的放大比例，还可以使用flex-shrink来指定当排列空间不足时，项目的缩小比例。

> **有2种情况**：
> 1、当多个子盒子的总宽度flex-basis<=父盒子时，使用的是flex-grow属性，按比例分配剩余空间；
> 2、当多个子盒子的总宽度flex-basis>父盒子时，使用的是flex-shrink属性，规则是
>     .box{
>       display:flex;
>       width:200px;
>       height:50px;
>     }
>     .left{
>       flex:3 2 50px
>     }
>     .right{
>       flex: 2 1 200px
>     }
>     
>
>     计算规则：
>       (1)计算超出父盒子的宽度：200+50-200 = 50;
>       (2).left需要减少：(50*2)/(50*2+200*1) * 50 = 50/3
>         .right需要减少：(200*1)/(50*2+200*1) * 50 = 100/3
>       (3)最终left宽度：50-50/3 = 100/3
>         right宽度：200-100/3 = 500/3
>       (4)left和right比例： 1:5

#### 10.响应式设计的概念及基本原理

> 响应式网站设计`（Responsive Web design`）是一个网站能够兼容多个终端，而不是为每一个终端做一个特定的版本。
>
> 关于原理： 基本原理是通过媒体查询`（@media）`查询检测不同的设备屏幕尺寸做处理。 关于兼容： 页面头部必须有mate声明的`viewport`。
>
> ```html
> <meta name="’viewport’" content="”width=device-width," initial-scale="1." maximum-scale="1,user-scalable=no”"/>
> ```

详细资料可以参考：
[《响应式布局原理》](https://blog.csdn.net/dreamerframework/article/details/8994741)
[《响应式布局的实现方法和原理》](http://www.mahaixiang.cn/wzsj/278.html)

#### 11. 如何根据设计稿进行移动端适配？

> 移动端适配主要有两个维度：
>
> - **适配不同像素密度，** 针对不同的像素密度，使用 CSS 媒体查询，选择不同精度的图片，以保证图片不会失真；
> - **适配不同屏幕大小，** 由于不同的屏幕有着不同的逻辑像素大小，所以如果直接使用 px 作为开发单位，会使得开发的页面在某一款手机上可以准确显示，但是在另一款手机上就会失真。为了适配不同屏幕的大小，应按照比例来还原设计稿的内容。
>
> 为了能让页面的尺寸自适应，可以使用 rem，em，vw，vh 等相对单位。

#### 12.移动端的布局用过媒体查询吗？

```
假设你现在正用一台显示设备来阅读这篇文章，同时你也想把它投影到屏幕上，或者打印出来，而显示设备、屏幕投影和打印等这些
媒介都有自己的特点，CSS就是为文档提供在不同媒介上展示的适配方法

当媒体查询为真时，相关的样式表或样式规则会按照正常的级联规被应用。当媒体查询返回假，标签上带有媒体查询的样式表仍将被
下载（只不过不会被应用）。

包含了一个媒体类型和至少一个使用宽度、高度和颜色等媒体属性来限制样式表范围的表达式。CSS3加入的媒体查询使得无需修改
内容便可以使样式应用于某些特定的设备范围。
```

详细资料可以参考：
[《CSS3@media 查询》](http://www.runoob.com/cssref/css3-pr-mediaquery.html)
[《响应式布局和自适应布局详解》](http://caibaojian.com/356.html)

#### 13.自定义属性

> 之前我们通常是在预处理器里才可以使用变量，而现在 CSS 里也支持了变量的用法。通过自定义属性就可以在想要使用的地方引用它。
>
> 自定义属性也和普通属性一样具有级联性，申明在 :root 下的时候，在全文档范围内可用，而如果是在某个元素下申明自定义属性，则只能在它及它的子元素下才可以使用。
>
> 自定义属性必须通过 `--x` 的格式申明，比如：--theme-color: red; 使用自定义属性的时候，需要用 var 函数。比如：
>
> ```css
> <!-- 定义自定义属性 -->
> :root {
>     --theme-color: red;
> }
> 
> <!-- 使用变量 -->
> h1 {
>     color: var(--theme-color);
> }
> ```
>
> <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ed8981b46bd3480cb76ce95cb4521023~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp" alt="img" style="zoom:50%;" />
>
> 上图这个是使用 CSS 自定义属性配合 JS 实现的动态调整元素的 box-shadow，具体可以看这个 [codepen demo](https://link.juejin.cn?target=https%3A%2F%2Fcodepen.io%2Fbulandent%2Fpen%2FGVjxLJ)。

#### 14.消除浏览器默认样式

> 针对同一个类型的 HTML 标签，不同的浏览器往往有不同的表现，所以在网站制作的时候，开发者通常都是需要将这些浏览器的默认样式清除，让网页在不同的浏览器上能够保持一致。
>
> 针对清除浏览器默认样式这件事，在很早之前 CSS 大师 Eric A. Meyer 就干过。它就是写一堆通用的样式用来重置浏览器默认样式，这些样式通常会放到一个命名为 reset.css 文件中。
>
> 除了 reset.css 外，后来又出现了 [Normalize.css](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fnecolas%2Fnormalize.css) 。关于 Normalize.css, 其作者 necolas 专门写了一篇文章介绍了它，并谈到了它和 reset.css 的区别。这个是他写那篇文章的翻译版：[让我们谈一谈 Normalize.css](https://link.juejin.cn?target=https%3A%2F%2Fjerryzou.com%2Fposts%2FaboutNormalizeCss%2F)。
>
> 文章介绍到：Normalize.css 只是一个很小的CSS文件，但它在默认的 HTML 元素样式上提供了跨浏览器的高度一致性。相比于传统的 CSS reset，Normalize.css 是一种现代的、为 HTML5 准备的优质替代方案，现在已经有很多知名的框架和网站在使用它了。
>
> Normalize.css 的具体样式可以看这里 [Normalize.css](https://link.juejin.cn?target=https%3A%2F%2Fnecolas.github.io%2Fnormalize.css%2Flatest%2Fnormalize.css)
>
> 区别于 reset.css，Normalize.css 有如下特点：
>
> - reset.css 几乎为所有标签都设置了默认样式，而 Normalize.css 则是有选择性的保护了部分有价值的默认值；
> - 修复了很多浏览器的 bug，而这是 reset.css 没做到的；
> - 不会让你的调试工具变的杂乱，相反 reset.css 由于设置了很多默认值，所以在浏览器调试工具中往往会看到一大堆的继承样式，显得很杂乱；
> - Normalize.css 是模块化的，所以可以选择性的去掉永远不会用到的部分，比如表单的一般化；
> - Normalize.css 有详细的说明文档；

#### 15.长文本处理

> **默认：字符太长溢出了容器**
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/40ade871ebd34a53a87b5fdac31190b8~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> **字符超出部分换行**
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7c5d1b30df0041258c0ae35a496d5e78~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> **字符超出位置使用连字符**
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9f85b1c16a5c4dbfa549650794659553~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> **单行文本超出省略**
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cb0075e0ceed4533b35b44db7e6a2b08~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> **多行文本超出省略**
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/88afb6a8f2824d4487267f6ff7f3e49c~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> 查看以上这些方案的示例： [codepen demo](https://link.juejin.cn/?target=https%3A%2F%2Fcodepen.io%2Fbulandent%2Fpen%2FabBrNby)

####  16.CSS 多列等高如何实现？

```
（1）利用padding-bottom|margin-bottom正负值相抵，不会影响页面布局的特点。设置父容器设置超出隐藏（overflow:
hidden），这样父容器的高度就还是它里面的列没有设定padding-bottom时的高度，当它里面的任一列高度增加了，则
父容器的高度被撑到里面最高那列的高度，其他比这列矮的列会用它们的padding-bottom补偿这部分高度差。

（2）利用table-cell所有单元格高度都相等的特性，来实现多列等高。

（3）利用flex布局中项目align-items属性默认为stretch，如果项目未设置高度或设为auto，将占满整个容器的高度
的特性，来实现多列等高。

```

> **方法一：padding + 负margin**
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/012fee13397f46c7ab1b4826b9d58c08~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> **方法二：设置父级背景图片**
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c61b2824b0b34c9090bc3f83b44e6644~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

详细资料可以参考：
[《前端应该掌握的 CSS 实现多列等高布局》](https://juejin.im/post/5b0fb34151882515662238fd)
[《CSS：多列等高布局》](https://codepen.io/yangbo5207/post/equh)

#### 17.三行布局（头尾定高主栏自适应）

> 列了 4 种方法，都是基于如下的 HTML 和 CSS 的，结合示例阅读效果更佳：[codepen demo](https://link.juejin.cn?target=https%3A%2F%2Fcodepen.io%2Fbulandent%2Fpen%2FyLVdpvr)
>
> ```html
> <div class="layout">
>     <header></header>
>     <main>
>         <div class="inner"></div>
>     </main>
>     <footer></footer>
> </div>
> html,
> body,
> .layout {
>     height: 100%;
> }
> body {
>     margin: 0;
> }
> header, 
> footer {
>     height: 50px;
> }
> main {
>     overflow-y: auto;
> }
> ```
>
> **方法一：calc**
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8930dba26767406c9154b03160a5f931~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> **方法二：absolute**
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3925987fb43245498483a9083fe6b4b4~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> **方法三：flex**
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/689b14dbb11e4c72a983ef0cd127fb9a~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> **方法四：grid**
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4a60294b8dc943378f9a851fc0c63afa~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 三.定位与浮动

#### 1.请解释一下为什么需要清除浮动？清除浮动的方式

> **浮动的定义：** 非IE浏览器下，容器不设高度且子元素浮动时，容器高度不能被内容撑开。 此时，内容会溢出到容器外面而影响布局。这种现象被称为浮动（溢出）。
>
> **浮动的工作原理：**
>
> - 浮动元素脱离文档流，不占据空间（引起“高度塌陷”现象）
> - 浮动元素碰到包含它的边框或者其他浮动元素的边框停留
>
> 浮动元素可以左右移动，直到遇到另一个浮动元素或者遇到它外边缘的包含框。浮动框不属于文档流中的普通流，当元素浮动之后，不会影响块级元素的布局，只会影响内联元素布局。此时文档流中的普通流就会表现得该浮动框不存在一样的布局模式。当包含框的高度小于浮动框的时候，此时就会出现“高度塌陷”。
>
> **浮动元素引起的问题？**
>
> - 父元素的高度无法被撑开，影响与父元素同级的元素
> - 与浮动元素同级的非浮动元素会跟随其后
> - 若浮动的元素不是第一个元素，则该元素之前的元素也要浮动，否则会影响页面的显示结构
>
> **清除浮动的方式如下：**
>
> - 给父级div定义`height`属性
> - 最后一个浮动元素之后添加一个空的div标签，并添加`clear:both`样式
> - 包含浮动元素的父级标签添加`overflow:hidden`或者`overflow:auto`
> - 使用 :after 伪元素。由于IE6-7不支持 :after，使用 zoom:1 触发 hasLayout**
>
> ```css
> .clearfix:after{
>     content: "\200B";
>     display: table; 
>     height: 0;
>     clear: both;
>   }
>   .clearfix{
>     *zoom: 1;
>   }
> ```
>
> #### BFC 清除浮动
>
> 前面介绍 BFC 的时候提到过，计算 BFC 高度的时候浮动子元素的高度也将计算在内，利用这条规则就可以清楚浮动。
>
> 假设一个父元素 parent 内部只有 2 个子元素 child，且它们都是左浮动的，这个时候 parent 如果没有设置高度的话，因为浮动造成了高度坍塌，所以 parent 的高度会是 0，此时只要给 parent 创造一个 BFC，那它的高度就能恢复了。
>
> 而产生 BFC 的方式很多，我们可以给父元素设置overflow: auto 来简单的实现 BFC 清除浮动，但是为了兼容 IE 最好用 overflow: hidden。
>
> ```css
> .parent {
>     overflow: hidden;
> }
> ```
>
> 通过 overflow: hidden 来清除浮动并不完美，当元素有阴影或存在下拉菜单的时候会被截断，所以该方法使用比较局限。

#### 2.使用 clear 属性清除浮动的原理？

> 使用clear属性清除浮动，其语法如下：
>
> ```css
> clear:none|left|right|both
> ```
>
> 如果单看字面意思，clear:left 是“清除左浮动”，clear:right 是“清除右浮动”，实际上，这种解释是有问题的，因为浮动一直还在，并没有清除。
>
> 官方对clear属性解释：“**元素盒子的边不能和前面的浮动元素相邻**”，对元素设置clear属性是为了避免浮动元素对该元素的影响，而不是清除掉浮动。
>
> 还需要注意 clear 属性指的是元素盒子的边不能和前面的浮动元素相邻，注意这里“**前面的**”3个字，也就是clear属性对“后面的”浮动元素是不闻不问的。考虑到float属性要么是left，要么是right，不可能同时存在，同时由于clear属性对“后面的”浮动元素不闻不问，因此，当clear:left有效的时候，clear:right必定无效，也就是此时clear:left等同于设置clear:both；同样地，clear:right如果有效也是等同于设置clear:both。由此可见，clear:left和clear:right这两个声明就没有任何使用的价值，至少在CSS世界中是如此，直接使用clear:both吧。
>
> 一般使用伪元素的方式清除浮动：
>
> ```css
> .clear::after{  
>     content:'';  
>     display: block;   
>     clear:both;
> }
> ```
>
> **clear属性只有块级元素才有效的，而::after等伪元素默认都是内联水平**，这就是借助伪元素清除浮动影响时需要设置display属性值的原因。

#### 3.zoom:1 的清除浮动原理?

```
清除浮动，触发hasLayout；
zoom属性是IE浏览器的专有属性，它可以设置或检索对象的缩放比例。解决ie下比较奇葩的bug。譬如外边距（margin）
的重叠，浮动清除，触发ie的haslayout属性等。

来龙去脉大概如下：
当设置了zoom的值之后，所设置的元素就会就会扩大或者缩小，高度宽度就会重新计算了，这里一旦改变zoom值时其实也会发
生重新渲染，运用这个原理，也就解决了ie下子元素浮动时候父元素不随着自动扩大的问题。

zoom属性是IE浏览器的专有属性，火狐和老版本的webkit核心的浏览器都不支持这个属性。然而，zoom现在已经被逐步标
准化，出现在CSS3.0规范草案中。

目前非ie由于不支持这个属性，它们又是通过什么属性来实现元素的缩放呢？可以通过css3里面的动画属性scale进行缩放。
```

#### 4.什么是margin重叠问题？如何解决？

> **问题描述：** 两个块级元素的上外边距和下外边距可能会合并（折叠）为一个外边距，其大小会取其中外边距值大的那个，这种行为就是外边距折叠。需要注意的是，**浮动的元素和绝对定位**这种脱离文档流的元素的外边距不会折叠。重叠只会出现在**垂直方向**。
>
> **计算原则：** 折叠合并后外边距的计算原则如下：
>
> - 如果两者都是正数，那么就去最大者
> - 如果是一正一负，就会正值减去负值的绝对值
> - 两个都是负值时，用0减去两个中绝对值大的那个
>
> **解决办法：** 对于折叠的情况，主要有两种：**兄弟之间重叠**和**父子之间重叠** 
>
> （1）兄弟之间重叠
>
> - 底部元素变为行内盒子：`display: inline-block`
> - 底部元素设置浮动：`float`
> - 底部元素的position的值为`absolute/fixed`
>
> ```html
> <!DOCTYPE html>
> <html>
>     <head>
>         <meta charset=utf-8>
>         <style type="text/css">
>             * {
>                 margin: 0;
>                 padding: 0;
>             }
>             .top{
>                 width:100px;
>                 height:100px;
>                 margin:10px;
>                 background:blue;
>                 /*display:inline-block;*/
>                 
>             }
>             .bottom{
>                 width:100px;
>                 height:100px;
>                 margin:10px;
>                 background:red;
>                 float:left;
>                 /*position:absolute;*/
>             }
>         </style>
>     </head>
>     <body>
>         <section class="top"></section>
>         <section class="bottom"></section>
>     </body>
> </html>
> ```
>
> （2）父子之间重叠
>
> - 父元素加入：`overflow: hidden`
> - 父元素添加透明边框：`border:1px solid transparent`
> - 子元素变为行内盒子：`display: inline-block`
> - 子元素加入浮动属性或定位

#### 5.对 BFC 规范（块级格式化上下文：block formatting context）的理解？

> 先来看两个相关的概念：
>
> - Box: Box 是 CSS 布局的对象和基本单位，⼀个⻚⾯是由很多个 Box 组成的，这个Box就是我们所说的盒模型。
> - Formatting context：块级上下⽂格式化，它是⻚⾯中的⼀块渲染区域，并且有⼀套渲染规则，它决定了其⼦元素将如何定位，以及和其他元素的关系和相互作⽤。
>
> 块格式化上下文（Block Formatting Context，BFC）是Web页面的可视化CSS渲染的一部分，是布局过程中生成块级盒子的区域，也是浮动元素与其他元素的交互限定区域。
>
> 通俗来讲：BFC是一个独立的布局环境，可以理解为一个容器，在这个容器中按照一定规则进行物品摆放，并且不会影响其它环境中的物品。如果一个元素符合触发BFC的条件，则BFC中的元素布局不受外部影响。
>
> > `BFC`是一个完全独立的空间（布局环境），让空间里的子元素不会影响到外面的布局。
>
> **BFC 渲染规则**
>
> - 内部的盒子会在垂直方向，一个接一个地放置；
> - 盒子垂直方向的距离由 margin 决定，属于同一个 BFC 的两个相邻盒子的 margin 会发生重叠；
> - 每个元素的 margin 的左边，与包含块 border 的左边相接触(对于从左往右的格式化，否则相反)，即使存在浮动也是如此；
> - BFC 的区域不会与 float 盒子重叠；
> - BFC 就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
> - 计算 BFC 的高度时，浮动元素也参与计算。
>
> **创建BFC的条件：**
>
> - 根元素：html
> - 非溢出的可见元素：overflow 不为 visible(auto,hidden,scroll)
> - 设置浮动：float 属性不为 none
> - 设置定位：position 为 absolute 或 fixed
> - 定义成块级的非块级元素：display: inline-block/table-cell/table-caption/flex/inline-flex/grid/inline-grid
>
> **BFC的特点：**
>
> - 垂直方向上，自上而下排列，和文档流的排列方式一致。
> - 在BFC中上下相邻的两个容器的margin会重叠
> - 计算BFC的高度时，需要计算浮动元素的高度
> - BFC区域不会与浮动的容器发生重叠
> - BFC是独立的容器，容器内部元素不会影响外部元素
> - 每个元素的左margin值和容器的左border相接触
>
> **BFC的作用：**
>
> - **解决margin的重叠问题**：由于BFC是一个独立的区域，内部的元素和外部的元素互不影响，将两个元素变为两个BFC，就解决了margin重叠的问题。
>
> ```css
> （1）兄弟之间重叠
> - 底部元素变为行内盒子：`display: inline-block`
> - 底部元素设置浮动：`float`
> - 底部元素的position的值为`absolute/fixed`
> 
> （2）父子之间重叠
> - 父元素加入：`overflow: hidden`
> - 父元素添加透明边框：`border:1px solid transparent`
> - 子元素变为行内盒子：`display: inline-block`
> - 子元素加入浮动属性或定位
> ```
>
> - **解决高度塌陷的问题**：在对子元素设置浮动后，父元素会发生高度塌陷，也就是父元素的高度变为0。解决这个问题，只需要把父元素变成一个BFC。常用的办法是给父元素设置`overflow:hidden`。
>
> ```css
> .parent {
>     overflow: hidden;
> }
> ```
>
> - **创建自适应两栏布局**：可以用来创建自适应两栏布局：左边的宽度固定，右边的宽度自适应。
>
> ```css
> .left{
>      width: 100px;
>      height: 200px;
>      background: red;
>      float: left;
>  }
>  .right{
>      height: 300px;
>      background: blue;
>      overflow: hidden;
>  }
> 
> <div class="left"></div>
> <div class="right"></div>
> ```
>
> 左侧设置`float:left`，右侧设置`overflow: hidden`。这样右边就触发了BFC，BFC的区域不会与浮动元素发生重叠，所以两侧就不会发生重叠，实现了自适应两栏布局。

详细资料可以参考：
[《深入理解 BFC 和 MarginCollapse》](https://www.w3cplus.com/css/understanding-bfc-and-margin-collapse.html)
[《前端面试题-BFC（块格式化上下文）》](https://segmentfault.com/a/1190000013647777)

#### 6.IFC 是什么？

> IFC 的形成条件非常简单，块级元素中仅包含内联级别元素，需要注意的是当IFC中有块级元素插入时，会产生两个匿名块将父元素分割开来，产生两个 IFC。
>
> **IFC 应用场景**
>
> - 水平居中：当一个块要在环境中水平居中时，设置其为 inline-block 则会在外层产生 IFC，通过 text-align 则可以使其水平居中。
> - 垂直居中：创建一个 IFC，用其中一个元素撑开父元素的高度，然后设置其 vertical-align: middle，其他行内元素则可以在此父元素下垂直居中。

```
IFC指的是行级格式化上下文，它有这样的一些布局规则：

（1）行级上下文内部的盒子会在水平方向，一个接一个地放置。
（2）当一行不够的时候会自动切换到下一行。
（3）行级上下文的高度由内部最高的内联盒子的高度决定。
```

详细资料可以参考：
[《[译]:BFC 与 IFC》](https://segmentfault.com/a/1190000004466536#articleHeader5)
[《BFC 和 IFC 的理解（布局）》](https://blog.csdn.net/paintandraw/article/details/80401741)

#### 7.什么是层叠上下文？

```
层叠上下文，英文称作stacking context，是HTML中的一个三维的概念。如果一个元素含有层叠上下文，我们可以理解为这个元
素在z轴上就“高人一等”。

层叠上下文元素有如下特性：

（1）层叠上下文的层叠水平要比普通元素高（原因后面会说明）。
（2）层叠上下文可以阻断元素的混合模式。
（3）层叠上下文可以嵌套，内部层叠上下文及其所有子元素均受制于外部的“层叠上下文”。
（4）每个层叠上下文和兄弟元素独立，也就是说，当进行层叠变化或渲染的时候，只需要考虑后代元素。
（5）每个层叠上下文是自成体系的，当元素发生层叠的时候，整个元素被认为是在父层叠上下文的层叠顺序中。


层叠上下文的创建：

（1）页面根元素天生具有层叠上下文，称为根层叠上下文。根层叠上下文指的是页面根元素，可以看成是<html>元素。因此，页面中所有的元素一定处于至少一个“层叠结界”中。

（2）对于position值为relative/absolute以及Firefox/IE浏览器（不包括Chrome浏览器）下含有position:fixed声明的定位元素，当其z-index值不是auto的时候，会创建层叠上下文。Chrome等WebKit内核浏览器下，position:fixed元素天然层叠上下文元素，无须z-index为数值。根据我的测试，目前IE和Firefox仍是老套路。

（3）其他一些CSS3属性，比如元素的opacity值不是1。
```

#### 8.元素的层叠顺序？

层叠顺序，英文称作 stacking order，表示元素发生层叠时有着特定的垂直显示顺序。

![层叠顺序](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/note-15.png)

> 下面是盒模型的层叠规则：于上图，由上到下分别是： 
>
> （1）背景和边框：建立当前层叠上下文元素的背景和边框。
>
>  （2）负的z-index：当前层叠上下文中，z-index属性值为负的元素。 
>
> （3）块级盒：文档流内非行内级非定位后代元素。
>
> （4）浮动盒：非定位浮动元素。 
>
> （5）行内盒：文档流内行内级非定位后代元素。 
>
> （6）z-index:0：层叠级数为0的定位元素。 
>
> （7）正z-index：z-index属性值为正的定位元素。
>
> **注意:** 当定位元素z-index:auto，生成盒在当前层叠上下文中的层级为 0，不会建立新的层叠上下文，除非是根元素。

#### 9.什么是层叠水平？层叠准则？

> 层叠水平，英文称作stacking level，决定了同一个层叠上下文中元素在z轴上的显示顺序。
>
> 显而易见，所有的元素都有层叠水平，包括层叠上下文元素，也包括普通元素。然而，对普通元素的层叠水平探讨只局限在当前层叠上
> 下文元素中。

> 层叠准则：
>
> （1）谁大谁上：当具有明显的层叠水平标识的时候，如生效的z-index属性值，在同一个层叠上下文领域，层叠水平值大的那一个覆盖小的那一个。
>
> （2）后来居上：当元素的层叠水平一致、层叠顺序相同的时候，在DOM流中处于后面的元素会覆盖前面的元素。

#### 10. position的属性有哪些，区别是什么

![image-20220308150026017](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220308150026017.png)

> - **relative：** 元素的定位永远是相对于元素自身位置的，和其他元素没关系，也不会影响其他元素。如果对一个元素进行相对定位，它将出现在它所在的位置上。然后，可以通过设置垂直 或水平位置，让这个元素“相对于”它的起点进行移动。 在使用相对定位时，无论是 否进行移动，元素仍然占据原来的空间。因此，移动元素会导致它覆盖其它框。
>
>   ![image-20220308150124197](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220308150124197.png)
>
> - **fixed：** 元素的位置相对于浏览器窗口是固定位置，即使窗口是滚动的它也不会移动。Fixed 定 位使元素的位置与文档流无关，因此不占据空间。 Fixed 定位的元素和其他元素重叠。
>
>   ![image-20220308150241323](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220308150241323.png)
>
> - **absolute：** 元素的定位相对于前两者要复杂许多。如果为 absolute 设置了 top、left，浏览器会根据什么去确定它的纵向和横向的偏移量呢？答案是浏览器会递归查找该元素的所有父元素，如果找到一个设置了`position:relative/absolute/fixed`的元素，就以该元素为基准定位，如果没找到，就以浏览器边界定位。如下两个图所示：
>
>   ![image-20220308150258070](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220308150258070.png)
>   
>   **粘性定位 sticky**： 元素先按照普通文档流定位，然后相对于该元素在流中的 flow root（BFC）和 containing block（最近的块级祖先元素）定位。而后，元素定位表现为在跨越特定阈值前为相对定 位，之后为固定定位。
>   
>   **默认定位 Static**： 默认值。没有定位，元素出现在正常的流中（忽略 top, bottom, left, right 或者 z-index 声 明）。 inherit: 规定应该从父元素继承 position 属性的值。

#### 11.'display'、'position'和'float'的相互关系？

```
（1）首先我们判断display属性是否为none，如果为none，则position和float属性的值不影响元素最后的表现。

（2）然后判断position的值是否为absolute或者fixed，如果是，则float属性失效，并且display的值应该被
设置为table或者block，具体转换需要看初始转换值。

（3）如果position的值不为absolute或者fixed，则判断float属性的值是否为none，如果不是，则display
的值则按上面的规则转换。注意，如果position的值为relative并且float属性的值存在，则relative相对
于浮动后的最终位置定位。

（4）如果float的值为none，则判断元素是否为根元素，如果是根元素则display属性按照上面的规则转换，如果不是，
则保持指定的display属性值不变。

总的来说，可以把它看作是一个类似优先级的机制，"position:absolute"和"position:fixed"优先级最高，有它存在
的时候，浮动不起作用，'display'的值也需要调整；其次，元素的'float'特性的值不是"none"的时候或者它是根元素
的时候，调整'display'的值；最后，非根元素，并且非浮动元素，并且非绝对定位的元素，'display'特性值同设置值。

```

详细资料可以参考：
[《position 跟 display、margincollapse、overflow、float 这些特性相互叠加后会怎么样？》](https://www.cnblogs.com/jackyWHJ/p/3756087.html)

#### 12.absolute与fixed共同点与不同点

> **共同点：**
>
> - 改变行内元素的呈现方式，将display置为inline-block 
> - 使元素脱离普通文档流，不再占据文档物理空间
> - 覆盖非定位文档元素
>
> **不同点：**
>
> - abuselute与fixed的根元素不同，abuselute的根元素可以设置，fixed根元素是浏览器。
> - 在有滚动条的页面中，absolute会跟着父元素进行移动，fixed固定在页面的具体位置。

#### 13.对 sticky 定位的理解

> sticky 英文字面意思是粘贴，所以可以把它称之为粘性定位。语法：**position: sticky;** 基于用户的滚动位置来定位。
>
> 粘性定位的元素是依赖于用户的滚动，在 **position:relative** 与 **position:fixed** 定位之间切换。它的行为就像 **position:relative;** 而当页面滚动超出目标区域时，它的表现就像 **position:fixed;**，它会固定在目标位置。元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。这个特定阈值指的是 top, right, bottom 或 left 之一，换言之，指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。

#### 14.绝对定位元素与非绝对定位元素的百分比计算的区别

> * 绝对定位元素的宽高百分比是相对于临近的position不为static的祖先元素的padding box来计算的。
>
> * 非绝对定位元素的宽高百分比则是相对于父元素的content box来计算的。

#### 15.position:fixed;在 android 下无效怎么处理？

```
因为移动端浏览器默认的viewport叫做layout viewport。在移动端显示时，因为layout viewport的宽度大于移动端屏幕
的宽度，所以页面会出现滚动条左右移动，fixed的元素是相对layout viewport来固定位置的，而不是移动端屏幕来固定位置的
，所以会出现感觉fixed无效的情况。

如果想实现fixed相对于屏幕的固定效果，我们需要改变的是viewport的大小为ideal viewport，可以如下设置：

<metaname="viewport"content="width=device-width,initial-scale=1.0,maximum-scale=1.0,minimum-sca
le=1.0,user-scalable=no"/>
```

### 四.场景应用

#### 1. 实现一个三角形

> CSS绘制三角形主要用到的是border属性，也就是边框。
>
> 平时在给盒子设置边框时，往往都设置很窄，就可能误以为边框是由矩形组成的。实际上，border属性是右三角形组成的，下面看一个例子：
>
> ```css
> div {
>     width: 0;
>     height: 0;
>     border: 100px solid;
>     border-color: orange blue red green;
> }
> ```
>
> <img src="C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220308151624352.png" alt="image-20220308151624352" style="zoom:50%;" />

> 所以可以根据border这个特性来绘制三角形：所以可以根据border这个特性来绘制三角形：
>
> ```css
> div {    
>      width: 0;    
>      height: 0;    
>      border-top: 50px solid red;    
>      border-right: 50px solid transparent;    
>      border-left: 50px solid transparent;
> }
> 
> ```
>
> <img src="C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220308151812308.png" alt="image-20220308151812308" style="zoom:50%;" />
>
> ```css
> div {
>     width: 0;
>     height: 0;
>     border-top: 100px solid red;
>     border-right: 100px solid transparent;
> }
> ```
>
> <img src="C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220308152620539.png" alt="image-20220308152620539" style="zoom:50%;" />

#### 2.实现一个扇形

> 用CSS实现扇形的思路和三角形基本一致，就是多了一个圆角的样式，实现一个90°的扇形：
>
> ```css
> div{
>     border: 100px solid transparent;
>     width: 0;
>     heigt: 0;
>     border-radius: 100px;
>     border-top-color: red;
> }
> ```
>
> <img src="C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220308152730684.png" alt="image-20220308152730684" style="zoom:50%;" />

#### 3.实现一个宽高自适应的正方形

> - 利用vw来实现：
>
> ```css
> .square {
>   width: 10%;
>   height: 10vw;
>   background: tomato;
> }
> ```
>
> - 利用元素的margin/padding百分比是相对父元素width的性质来实现：
>
> ```css
> .square {
>   width: 20%;
>   height: 0;
>   padding-top: 20%;
>   background: orange;
> }
> ```
>
> - 利用子元素的margin-top的值来实现：
>
> ```css
> .square {
>   width: 30%;
>   overflow: hidden;
>   background: yellow;
> }
> .square::after {
>   content: '';
>   display: block;
>   margin-top: 100%;
> }
> ```

[《自适应正方形 demo 展示》](http://cavszhouyou.top/Demo-Display/AdaptiveSquare/index.html)

[《三角形 demo 展示》](http://cavszhouyou.top/Demo-Display/Triangle/index.html)

#### 4.画一条0.5px的线

> - **采用transform: scale()的方式**，该方法用来定义元素的2D 缩放转换：
>
> ```css
> transform: scale(0.5,0.5);
> ```
>
> - **采用meta viewport的方式**
>
> ```css
> <meta name="viewport" content="width=device-width, initial-scale=0.5, minimum-scale=0.5, maximum-scale=0.5"/>
> ```
>
> 这样就能缩放到原来的0.5倍，如果是1px那么就会变成0.5px。viewport只针对于移动端，只在移动端上才能看到效果

[《怎么画一条 0.5px 的边（更新）》](https://juejin.im/post/5ab65f40f265da2384408a95)

#### 5.设置小于12px的字体

> 在谷歌下css设置字体大小为12px及以下时，显示都是一样大小，都是默认12px。
>
> **解决办法：**
>
> - 使用Webkit的内核的-webkit-text-size-adjust的私有CSS属性来解决，只要加了-webkit-text-size-adjust:none;字体大小就不受限制了。但是chrome更新到27版本之后就不可以用了。所以高版本chrome谷歌浏览器已经不再支持-webkit-text-size-adjust样式，所以要使用时候慎用。
> - 使用css3的transform缩放属性-webkit-transform:scale(0.5); 注意-webkit-transform:scale(0.75);收缩的是整个元素的大小，这时候，如果是内联元素，必须要将内联元素转换成块元素，可以使用display：block/inline-block/...；
> - 使用图片：如果是内容固定不变情况下，使用将小于12px文字内容切出做图片，这样不影响兼容也不影响美观。

#### 6.一个自适应矩形，水平垂直居中，且宽高比为 2:1

```css
/*实现原理参考自适应正方形和水平居中方式*/
.box {
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
  margin: auto;

  width: 10%;
  height: 0;
  padding-top: 20%;
  background: tomato;
}
```

