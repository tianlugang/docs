# DOM元素的宽高

- `HTMLElement.offsetWidth` 返回一个元素的布局宽度, 包含元素的边框(`border`)、水平线上的内边距(`padding`)、竖直方向滚动条(`scrollbar`)（如果存在的话）、以及CSS设置的宽度(`width`)的值, 即元素的`border-box`的宽度
- `HTMLElement.clientWidth`  表示元素的内部宽度，以像素计。内联元素以及没有 CSS 样式的元素的 clientWidth 属性值为 0，包括内边距 `padding`，但不包括边框 `border`、外边距 `margin` 和垂直滚动条（如果有的话）。
- `HTMLElement.scrollWidth` 表示元素内容宽度，包括由于overflow溢出而在屏幕上不可见的内容。它的值等于元素在不使用水平滚动条的情况下，适合视口中的所有内容所需的最小宽度。 宽度的测量方式与`clientWidth`相同：它包含元素的内边距`padding`，但不包括边框，外边距或垂直滚动条（如果存在）。 它还可以包括伪元素的宽度，例如`::before`或`::after`。 如果元素的内容可以适合而不需要水平滚动条，则其`scrollWidth`等于`clientWidth`
 

---

* `clientWidth` = `width` + `左右padding`
* `clientHeigh` = `height` + `上下padding` 
* `clientTop` = `border-top-width` (上边框的宽度)
* `clientLeft` = `border-left-width`(左边框的宽度)

---

* `offsetWidth` = `width` + `左右padding` +`左右border`
* `offsetHeith` = `height` + `上下padding` + `上下border`
* `offsetTop`：当前元素 上边框 外边缘 到 最近的已定位父级（`offsetParent`） 上边框 内边缘的距离。如果父级都没有定位，则分别是到body 顶部 和左边的距离
* `offsetLeft`：当前元素 左边框 外边缘 到 最近的已定位父级（`offsetParent`） 左边框 内边缘的距离。如果父级都没有定位，则分别是到body 顶部 和左边的距离

---

* `scrollWidth`：获取指定标签内容层的真实宽度（可视区域宽度+被隐藏区域宽度）。
* `scrollHeight`：获取指定标签内容层的真实高度（可视区域高度+被隐藏区域高度）
* `scrollTop` : 内容层顶部 到 可视区域顶部的距离。
```js
    var scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop;
    window.addEventListener('scroll', ()=>{
        var scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop;
    });
```        
* `scrollLeft`:内容层左端 到 可视区域左端的距离. 