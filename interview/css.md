# CSS 面试题

1. 阐述盒模型

    - content-box 和 border-box。  
    - 前者的 width 属性就是内容的宽度。  （标准盒模型）
    - 后者的 width 属性等于内容的宽度+padding+border。（ height 属性同理)

2. 实现垂直居中的方式

    - table 自动居中
    - inline 元素 vertical-align:middle
    - 宽高固定直接上 margin-top
    - transform: translate(-50%, -50%)
    - 绝对定位
    - flex 布局，align-items: center

3. flex 布局

    flex 的主轴是 flex-direction 所在的轴（默认是 row)，交叉轴是垂直的轴。
    justify-content: 调整主轴方向元素布局.
    align-items: 调整交叉轴方向元素布局.
    flex-wrap: wrap 换行

4. BFC 是什么

    BFC 的英文是 block formatting context，中文叫做块级格式化上下文。BFC 最重要的原则是，如果一个元素具有 BFC，那么内部元素再怎么折腾都不会影响外部的元素。因此 BFC 具有一些 CSS 的表现特性，比如 不会发生 margin 合并，因为边距合并内部元素就跑到 BFC 外面去了，会影响外面的元素。还有 BFC 也可以用来清除浮动，因为浮动带来父元素塌陷，肯定会影响后面元素的高度计算，这样是有违 BFC 的原则的。因此 BFC 可以包裹住浮动元素，撑开高度。

    常见的触发 BFC 的方式有：

    -  <html> 根元素
    - float 为 left,right
    - overflow 为 hidden,auto,scroll
    - position 为 absolute,fixed

5. CSS 选择器优先级

    i. 大体上是：!important > id > class > tag(标签)
    ii. 相同的权重：内联样式 > 嵌入样式（当前文件）> 外部样式（外部文件）
    iii. 写在后面的样式会覆盖同权重的前面的样式

6. 清除浮动

    ```
    .clearfix::after {
        content: '',
        display: block,
        clear: both
    }
    ```
7. 如何实现一个三角形

    i. 宽高为 0
    ii. 透明 border
    iii. 原理是什么

8. 兼容性处理...
    