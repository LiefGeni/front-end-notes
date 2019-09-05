# HTTP 面试题

1. 状态码有哪些

    - 200 成功
    - 404 not found
    - 403 没有权限请求(跨域会 403)
    - 500 服务器故障
    - 301 永久重定向，浏览器会记住
    - 302 临时重定向
    - 304 content not modified

2. 方法有哪些

    head get post put delete options

4. 缓存

    - 协商缓存

        i. Last-Modified & If-Modified-Since 请求头

        ii. Etag & If-None-Match 请求头

        iii. 304 状态码

    - 强制缓存

        i. expires (http1.0 规范)

            a. 客户端时间，易更改

            b. 和服务器时间不一定一致
        
        ii. Cache-Control (http1.1 规范)

            a. max-age（单位为秒）
            b. no-cache（不用cache-control的方式做前置验证，每次都要用Etag或者Last-Modified字段来向服务器验证缓存是否更新）
            c. no-store（绝对不缓存任何东西，每次请求都是新的，不附带Etag）
            d. public / private

    - 强制缓存 > 协商缓存 ; Etag > Last-Modified

    - PWA 和 service worker(这是什么东西???)


5. get 和 post 的区别 

    1. 语义的不同，get 是向服务器要东西，post 是给服务器一个东西
    2. get 请求的参数是跟在 url 地址后面的，长度不会太长，否则会被浏览器截断；post 请求的参数都是放在请求体里
    3. get 请求的历史会被浏览器记住，所以参数容易暴露
    4. get 请求应该是幂等的，不管请求多少次没有副作用，不会对资源造成影响或者修改；post 请求不是

8. 什么是跨域，怎么实现跨域

    不符合同源协议的请求都是跨域的。
    - 同协议
    - 同端口
    - 同域名
    
    实现跨域：
    - jsonp
        - `<script>` 标签 + `callback` 只支持 get 请求
    - cors 跨域资源共享
        - 简单情况
            - 请求方法：get post head <br>
            - 处理方式：添加 origin 头、Access-Control-Allow-Origin 头
        - 复杂情况
            - 请求方法：put delete 或者 content-type: application/json
            - 处理方式：浏览器先发 options 方法预检一下（包含 origin 头，Access-Control-Request-Method 头），如果预检成功会带有，服务器返回会带有 Access-Control-Allow-Origin 头，如果没有，说明预检失败。options 方法成功之后，就会发送正常的 http 请求（带 origin 头），服务器也会正常返回。（带 Access-Control-Allow-Origin 头)
        - 注意事项：
            - cors 请求默认不发送 cookie ，如果要发送 cookie 的话，服务器要指定 - Access-Control-Allow-Credentials: true，并且 ajax 请求要显示指定 xhr.withCredentials = true。如果请求带有 cookie，name Access-Control-Allow-Origin 不能为 *，必须指定明确的和请求一致的域名。
    - jsonp 和 cors 比较：
        jsonp 功能单一，只支持 get 请求，但是浏览器适用范围广。cors 更为强大

6. https 和 http

    1. http 的报文是明文传输的， https 是加密后的，即便报文被抓包截获也看不懂，所以 https 更安全

    2. https 为什么是安全的

        1. 报文加密

        2. 怎么加密的？

7. http2 新特性(相比 http1.1 )

    1. 多路复用

    2. 服务端推送(server push)

    3. 强制开启 https

7. ssl

8. 三次握手四次挥手

9. keepalive

7. cookie   V.S. session

    - cookie: 

        a. cookie 是服务器发给浏览器的一段数据

        b. HTTP 响应通过 Set-Cookie 设置 Cookie，如果要防止 js 修改 cookie 的话，加一个 http-only

        c. cookie 一般用来记录用户信息，随着请求一起发送到服务器

        d. cookie 大小一般比较小（4k以下）, 存在浏览器
        
    - session

        a. session 用来记录一段时间内浏览器和服务器的会话
        
        b. session 一般通过在 cookie 中记录 sessionID 来实现, session 存在服务器

        c. sessionID 一般是随机数


8. localStorage V.S. sesstionStorage

    - localStorage
        
        存储在浏览器的数据，即便浏览器窗口关闭数据也还存在。一般大小是 5mb，只能存字符串数据，因此复杂对象都要 JSON.stringify() 

    - sessionStorage（这是个什么东西???)

        sessionStorage 会在 session 结束之后过期

9. xss 

    1. 用 innerText 不要用 innerHTML

    2. 转义字符

        ```
        把 > 换成 &gt; < 换成 &lt; & 换成 &amp; ' 换成 $#39; ' 换成 &quot;
        ```

    3. （深入）前端转义或者后端转义有区别吗？

10. csrf

    - 是什么，怎么产生的

    - 用 csrf_token 解决



