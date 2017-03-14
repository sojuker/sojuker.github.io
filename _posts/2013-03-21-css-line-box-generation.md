说到CSS的line box，相关属性有 font-size、line-height、vertical-align、以及inline-block 的 margin box。

## 1、从字体大小 到 行内框(inline box)

让我们先从font-size说起。

在CSS规范中，每选用一个字体，我们通过设定其 font-size 值，实际上是确定了该字体的 em box。比如我们设定字体大小为 font-size: 14px，这就指定了我们选用大小为14px 的 em box 来进行网页字体的渲染。这里的14px并不是指实际字符的所占用的空间。

什么是 content box。

一般来说，在指定的 font-size 的情况下，大部分字体的 em box 的高度能够足够显示出该字体中的所有字符，但是还是有一些字体（比如通用的手写体），其显示字符所占用的高度会超过其 em box 的高度。 而 content box 的高度就取决于指定 font-size下，em box 的高度和实际显示字符所占用的高度。

The height of the content area should be based on the font, but this specification does not specify how. A UA may, e.g., use the em-box or the maximum ascender and descender of the font.

什么是行高 line-height。

行高是一个文本属性，用于定义相邻文本行之间的间隔。我们将设定的行高值与该行文本的 content box 相减，得到的就是传统排版行业中的 leading。 比如我们定义了 line-height:20px; font-size:14px 。 我们假设这次选用的字体的 content box 大于 em box，假设是16px。  那么，行高减去 content box，就是 4px 的leading。

在CSS中，leading 将会被平分为两部分，一部分附加在 content box 的上方，另一部分在 content box 的下方。 在这个栗子中，将会有2px的 half leading被分配在上方，另一个2px分配在下方。

我们将 content box 外加上下的 half leading 的这个盒子称为什么呢？没错，这就是 inline box。

inline box is the box described by the addition of the leading to the content area. For nonreplaced elements, the height of the inline box of an element will be exactly equal to the value for line-height.

让我们重新梳理一下思路

- 由 定义的 font-size值我们获得了 em box；
- 由 特定大小的 em box 和特定的字体，我们得到其对应的 content box；
- 由 定义的 line-height值和确定的content box值，我们得到了 leading，并进一步确定 half leading；
- 由 content box 和上下的 half leading，我们得到了 inline box。

附录：

1. the content area is analogous to the content box of a block-level element.
2. padding, borders and margins on nonreplaced elements have no vertical effect on inline elements or the boxes they generate; that is , they do not affect the height of an element's inline box.
3. padding, borders and margins on replaced elements do affect the height of the inline box for that element.

将非置换元素的 content area 定义为其 margin box。

## 2、什么是行

现在我们知道了什么是 inline box，就可以进一步理解什么是行框 —— `line box`。行框是指正好包含了该文本行中所有的行内框的一个 box，行框的顶部与该行内最高的行内框的顶部对齐，行框的底部与该行内最低的行内框的底部对齐。

那么行内能够包含哪些 CSS 元素呢。 一般来说，我们最为常见的元素就是文本了。除了文本之外，替换元素（比如图片 image、表单输入项 input）也可以出现在行内，另外，我们在CSS定义的 display: inline-block 可以出现在行内。

对于文本来说，我们才有 font-size、em box、leading、line-height 这些概念。
但是对于如 image 又或者是 inline-block 元素来说呢，又应该有什么样的机制来处理其 inline box 这个概念呢 ？

CSS规范给出了答案。

对于替换元素 和 inline-block 元素来说， 其 inline box 就是其 margin box。也就是说我们通过 image 元素导入一个外部图片时，假设其高度值就是固有高度40px（对于图片来说，在不设定宽度 和 高度的情况下，使用的固有高度），如果我们对图片的 padding、border 和 margin 都设定为0，其 inline box 就是40px。 这时，我们希望设定其margin为10px，border为1px，padding为3px，那么其 inline box 的高度就是 40+（1+3+10）*2 = 68px。

![1.jpg]({{ site.url }}/assets/2013-03-21-css-line-box-generation-study/1.jpg)

对于替换元素 和 inline-block 元素来说，其 content area 就是其 margin box。 尽管我们可以给置换元素设定 行高 line-height，但是行高本身并不起作用（你可以设置一个超大的行高，比如1234px 测试一下），但是行高会影响其他的因素，我们将在下一节继续探索。

## 3、什么是基线

各位看官可能注意到了，我上面的截图中有个基线对齐。 那么，现在是时候让我们来说说什么是基线。

![2.jpg]({{ site.url }}/assets/2013-03-21-css-line-box-generation-study/2.jpg)

我试着用图片来说明。 一般来说，在浏览器中文本的默认对齐方式就是基线对齐，而上图就是不同大小的字体基线对齐的一个效果。基线的概念，其实是为了描述英文文本（西方文字）而引入的概念，对于中文文字也有基线的概念，但一般来说我们并不关注。

还有一个问题，就是置换元素是没有基线的。 这个我们如何进行基线对齐呢，CSS规范规定了对于置换元素或是inline-block元素来说，与基线有关的对齐方式都使用该元素的 inline box 的底部。

## 4、垂直对齐方式

> The following values only have meaning with respect to a parent inline element, or to the strut of a parent block container element.

> On a block container element whose content is composed of inline-level elements, 'line-height' specifies the minimal height of line boxes within the element. The minimum height consists of a minimum height above the baseline and a minimum depth below it, exactly as if each line box starts with a zero-width inline box with the element's font and line height properties. We call that imaginary box a "strut." (The name is inspired by TeX.).

### 基线对齐 baseline：
- baseline forces the baseline of an element to align with the baseline of its parent.
- 基于基线的其他对齐方式：
    - sub、super、`<length>`、`<percentage>`
    - 指定了对齐方式的元素的基线与它父元素的基线在垂直方向上的距离。
    - 注意1：sub 和 super 的移动距离 根据 浏览器互有差异，因此CSS规范并没有定义。
    - 注意2：百分比相对于指定元素的 line-height 值，这就是置换元素为什么还需指定 line-height 的原因。

### 顶部对齐 top ：
- top aligns the top of the element's inline box with the top of the line box.

### 文本顶部对齐 text-top:
- the top of the aligned element's inline box is aligned with the top of the default text box.（this default box is derived from the font-size of the parent element）
- 注意1：默认文本框就是文本的 content area。
- ![3.jpg]({{ site.url }}/assets/2013-03-21-css-line-box-generation-study/3.jpg)

### 居中对齐 middle :
- middle aligns the middle of an inline element's box with a point that is 0.5ex(x-height) above the baseline of the parent element.
- 注意1：由于许多浏览器不能计算ex的高度，常使用 ex = em * 0.5 来近似处理，因此可能跟预期有出入；
- ![4.png]({{ site.url }}/assets/2013-03-21-css-line-box-generation-study/4.png)

