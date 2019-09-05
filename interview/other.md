# 其他发散题

1. 从输入 URL 到页面展现中间发生了什么

    i. DNS 查询 
        
        a. DNS 缓存

    ii. 建立 TCP 连接（三次握手）
    
        b. 连接复用(keepalive)

    iii. 发送 HTTP 请求（请求的四部分）

    iv. 后台处理请求

        a. 监听 80 端口

        b. 路由

        c. 渲染 HTML 模板

        d. 生成响应

    v. 发送 HTTP 响应

    vi. 关闭 TCP 连接（四次挥手）

    vii. 解析 HTML

    viii. 下载 CSS（缓存
    
    ix. 解析 CSS

    x. 下载 JS（缓存
    
    xi. 解析 JS
    
    xii. 下载图片
    
    xiii. 解析图片
    
    xiv. 渲染 DOM 树
    
    xv. 渲染样式树
    
    xvi. 执行 JS

2. 对前端工程化的理解

    - 模块化
    - 组件化
        i. 轮子
        ii. 抽取业务性质相似的组件
    - 规范化
        i. 多人协作
        ii. 代码规范
    - 自动化
        i. 资源打包
        ii. 部署

3. seo 优化

    a. ssr 服务端渲染(不了解...)

4. 浏览器解析 html 的过程

    1. 解析 html，生成 DOM 树

    2. 解析 css， 生成 CSSOM 树

    3. 合并两棵树，生成 render 树

    4. layout

    5. paint
    
5. 如何解析 js 和 css 

    1. 解析 js

        1. 立即执行
        
        `<script src='a.js'>` 遇到该标签时，浏览器会阻塞 DOM 树的构建，然后请求 js 脚本，当下载完成之后立即执行 js 脚本，然后再次构建 DOM 树

        为什么 js 会阻塞 DOM 树的构建，因为 js 可能会对 DOM 树进行操作，浏览器不想构建完了之后被 js 一顿更改，然后又要重新构建 DOM 树

        2. 推迟执行 defer
        
        `<script defer src='a.js'>` defer 关键字使得请求 js 脚本不会阻塞 DOM 树的构建，当下载完毕之后，等 DOM 树构建完成之后再执行 js（执行 js 会阻塞 DOM 树的构建）

        3. 尽快执行

        `<script async src='a.js'>` async 关键字使得 请求 js 也不会阻塞 DOM 树的构建，但是当下载完成之后，会马上执行 js（执行 js 会阻塞 DOM 树的构建）

    0. 为了不让 js 阻塞 DOM 树的构建，推荐 js 放在下面， css 放在上面

    1. css 是并行加载，js 是串行加载

    3. css 下载解析会不会阻塞DOM树的构建？
        
        - 不会阻塞DOM树的构建。
        
        - 会影响 DOM 树的渲染

            - chrome 浏览器: css 加载完成之前 DOM 树不会渲染，会出现**白屏**现象

            - firefox 浏览器：css 加载完成之前 DOM 树仍会渲染到屏幕上，但是没有样式，出现**无样式内容闪烁**

6. 前端性能优化

    1. 常规原则

        - js 放在下面，css 放在上面

        - 压缩文件，js，css，图片

        - 合并请求

        - 抽取代码

            - 公共 css

            - 编写可复用的组件

        - 开启缓存

        - cdn(不会就别坑自己...)

    2. 具体经历（举例）

        > 公众号手机端客户申请材料页，上传图片特别多，进入的时候展示怎么做的优化

# 刁钻代码题

1. map加parseInt

    ```
    [1,2,3].map(parseInt)

    parseInt(1,0, array) // 1
    parseInt(2,1, array) // NaN
    parseInt(3,2, array) // NaN
    ```

2. (a == 1 && a == 2 && a == 3) 可能为 true 吗？

    ```
    a = {
        value: 0,
        toString(){
            a.value += 1
            return a.value 
        }
    }
    ```
3. a.x 是什么

    ```
    var a = { name: 'jack' }

    a.x = a = {}

    // a.x 是什么? undefined
    // 等同于 var b = a = { name: 'jack'}
    // a = {}
    // b.x = a
    // b = {
        name: 'jack',
        x: {} // a
    }
    // a 现在是一个空对象， a.x 自然是 undefined
    ```