# HTML 面试题

1. 如何理解 HTML 语义化

    正确的标签做正确的事情。`h1` 到 `h6`表示标题，`p` 表示段落，`article` 表示文章这种。
    
    前端布局经历 `table` 布局，`div+class` 布局（换汤不换药），再到现在文档语义化，使页面结构和内容更清晰，也有利于搜索引擎解析
    


2. meta viewport 有什么用，怎么写

    `<meta name="viewport" content="width=device-width, initial-scale=1">`

    定义视口宽度

    `<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">`

    可以在移动端页面中防止用户缩放页面。


3. 列举 html5 标签

    - header footer main section article
    - canvas audio radio

4. 什么是 repaint， 什么是 reflow



# DOM 面试题

1. e.target 和 e.currentTarget

    e.target 事件发生的元素
    e.currentTarget 事件监听绑定的元素

2. 事件委托

    对于 ul>li 的点击事件，可以把事件监听绑定在ul身上。这样做的好处是：1.不用给每个li绑定事件监听 2.如果li是动态新增的，也可以达到监听的效果。

    ```
    ul>li*5>span

    function listen(element, eventType, selector, fn){
        element.addEventListener(eventType, e => {
            let el = e.target
            while(!el.matches(selector)){
                if(element === el){
                    el = null
                    break
                }
                el = el.parentNode
            }
            el && fn.call(el, e, el)
        })
    }
    ```

3. 事件模型

    1. 捕获和冒泡

    2. 如果这个元素是被点击的元素，那么捕获不一定发生在冒泡之前，顺序是由监听顺序决定的
    
    3. 为什么大多数事件监听在冒泡阶段？

4. 移动端的触摸事件

    i. touchstart touchmove touchend touchcancel
    ii. 模拟 swipe 事件：记录两次 touchmove 的位置差，如果大于零，说明向右滑了



