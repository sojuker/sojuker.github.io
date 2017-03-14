## Window Interface

**innerWidth/Height**，视口`viewport`大小, 包含滚动条。
**pageXOffset和pageYOffset**，视口中整个文档 `document` 滚动值

## Document Interface

**elementFromPoint(x, y)**
返回给定坐标处所在的元素。通过模拟点击动作获得该元素，相对于 viewport。

## Element Interface

**getBoundingClientRect()**

元素 `border area` 相对于 `viewport` 的定位信息, 返回拥有 left、top、right、bottom 属性的对象。

**clientLeft 和 clientTop**

> Return the computed value of the border-left-width property
> plus the width of any scrollbar rendered between the left padding edge and the left border edge,

**clientWidth和clientHeight**

> If the element is the root element(document.documentElement), return the viewport width excludinganythe rendered scrollbar.
>
> OR Return the width of the padding edge, excluding the width of any rendered scrollbar

**scrollLeft和scrollTop**
元素的滚动距离信息，该元素有滚动条时才有意义。

**scrollWidth和scrollHeight**
元素 `padding edge` 的实际宽高，有滚动条时有特殊行为。

## HTMLElement Interface

**offsetParent**
The computed value of the `position` property is not static *OR* the HTML `body` element.

> https://www.w3.org/TR/cssom-view-1/#dom-htmlelement-offsetparent

**offsetLeft 和 offsetTop**
元素相对于 offsetParent 的偏移值, 基准为 offsetParent的 `padding edge` 和元素的 `border edge`。
这里跟 position left 不一样

> top
>
> the property specifies how far an absolutely positioned box's top margin edge
> is offset below the top edge of the box's containing block.
> If the element has 'position: absolute', the containing block is formed by the padding edge of the offsetParent.

**offsetWidth和offsetHeight**
元素的 `border box` 尺寸，含滚动条。

## MouseEvent Interface

**clientX, clientY**
鼠标相对视口 `viewport` 的坐标。

**offsetX, offsetY**
鼠标相对于当前被点击元素(e.target) `padding edge` 的偏移值。

**pageX, pageY**
鼠标相对于文档 `document` 的坐标。

see: [w3c 文档](http://www.w3.org/TR/cssom-view/)
