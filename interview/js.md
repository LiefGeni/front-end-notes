# 原生 JS 面试题

1. js 基本数据类型

    string number bool undefined null object symbol

    object 包含了数组、函数、正则、日期等对象


1. ES 6 语法
    - let const 块级作用域
    - 解构赋值
    - 展开运算符
    - 类相关 class extends super
    - 模块化 import export 
    - 箭头函数
    - 生成器 generator
    - promise
    - 模版字符串 
    - 数组的扩充方法(find, findIndex)

2. Promise, Promise.all, Promise.race 分别怎么用

    ```
        var p = new Promise(function(resolve, reject){
            if(成功了){
                resolve()
            }else{
                reject()
            }
        })
        p().then(()=>{
            // 成功之后继续下一步
        },()=>{
            // 失败了怎么处理
        }).then(成功函数2, 失败函数2).catch(()=>{
            // 异常处理
        })

    ```
    Promise.all 适合同时请求多个不相干的异步操作，只有等都成功了才继续下一步

    ```
    Promise.all([p1, p2, p3]).then(()=>{
        // p1, p2, p3 都成功了
    }).catch((error)=>{
        // 异常处理
    })
    ```
    Promise.race 适合同时请求多个不相干的异步操作，只要有一个成功就继续下一步

    ```
    Promise.race([p1,p2,p3]).then(()=>{
        // p1, p2, p3 任一成功
    },()=>{
        // 失败了怎么处理
    }).catch((error)=>{
        // 异常处理
    })
    ```

3. 手写 Promise

    ```
    function Promise(fn){
        var callbacks = []
        var state = 'pending'
        var value = value

        function resolve(newValue){
            value = newValue
            state = 'fullfilled'
            setTimeout(function(){ // 异步调用，保证 resolve 的时候 callbacks 有东西
                callbacks.forEach(function(callback){
                    callback(value)
                })
            })
        }
        function reject(reason){
            value = reason
            state = 'rejected'
            setTimeout(function(){ // 异步调用，保证 resolve 的时候 callbacks 有东西
                callbacks.forEach(function(callback){
                    callback(value)
                })
            })
        }

        function handle(handler){
            if(state === 'pending'){
                callbacks.push(handler)
            } else if(state === 'fullfilled') {
                // 异常处理
                try {
                    if(handler.onResolved){
                        var ret = handler.onResolved(value)
                        handler.resolve(ret)
                    }else{
                        handler.resolve(value)
                    }
                } catch(e) {
                    handler.reject(e)
                }
                
            } else {
                if(handler.onRejected){
                    // 异常处理
                    try {
                        // promise 异常处理责任制的体现
                        // 如果上一个promise有异常处理函数，下一个promise直接resolve
                        var ret = handler.onRejected(value)
                        handler.resolve(ret)
                    } catch(e) {
                        handler.reject(e)
                    }
                    
                }else{
                    // 没有的话就reject
                    handler.reject(value)
                }
            }
        }

        // 一般都是同步绑定 then 回调函数，因此 resolve 和 reject 都要用 setTimeout 推到下一轮事件循环
        this.then = function(onResolved, onRejected){
            // 返回新的 promise 对象实现链式调用
            return new Promise(function(resolve, reject){
                handle({
                    onResolved,
                    resolve,
                    onRejected,
                    reject,
                })
            })
        }
        
        fn(resolve, reject)
    }
    
    ```

4. promise的异常处理？链式promise调用的异常处理？

    promise 的异常处理是责任制的，如果上一个 promise 有异常处理，那么下一个 promise 就继续 resolve，否则会继续往后寻找异常处理函数，直到找到为止。

4. 手写函数防抖和函数节流
    
    i. 函数防抖（连续触发都清除，最后只留一次）
    ```
    function debounce(func, delay){
        var timer;
        return function(e){
            timer && window.clearTimeout(timer)
            timer = setTimeout(()=>{
                func.apply(this, arguments)
            }, delay)
        }
    }
    var validate = debounce(function(e){
        console.log('验证输入: ', e.target.value)
    },300)
    
    // 绑定监听
    document.querySelector('input').addEventListener('input', validate)

    // 应用场景：input 输入验证，提交按钮的点击事件

    // 简化版 口诀：带着一起做
    function fn(){
        // 要做的事情
    }
    input.oninput = function(){
        var timer
        return function(){
            timer && window.clearTimeout()
            timer = setTimeout(()=>{
                fn()
            },300)
        }
    }
    ```



    ii. 函数节流（对于高频发生的事件，以一定的频次触发事件监听，让方法一段时间内只执行一次)
    ```
    function throttle(fn, threshhold){
        var timer
        var start = new Date
        var threshhold = threshhold | 300
        
        return function(){
            var context = this, args = arguments, cur = new Date() - 0
            timer && window.clearTimeout(timer) // 总是去掉事件回调
            if(cur - start >= threshhold){
                fn.apply(context, args)
                start = cur
            }else{
                timer = setTimeout(function(){
                    fn.apply(context, args)
                }, threshhold)
            }
        }
    }
    var mousemove = throttle(function(e){
        console.log(e.pageX, e.pageY)
    })
    // 绑定监听
    document.querySelector('#container').addEventListener('mousemove', mousemove)

    // 简化版，口诀：冷却时间
    function fn(){
        // 要做的事情
    }
    container.onmousemove = function(){
        var cd = false, timer
        return function(){
            timer && window.clearTimeout
            if(!cd){
                cd = true
                timer = setTimeout(()=>{
                    fn()
                    cd = false
                },300)
            }
        }
    }
    ```

5. 手写 ajax

    ```
    var xhr = new XMLHttpRequest()
    xhr.open('GET', '/xxx')
    xhr.onreadystatechange = function(){
        if(xhr.readyState === 4){
            console.log('请求完成')
            if(xhr.response.status >= 200 && xhr.response.status < 300){
                console.log('请求成功')
            }
        }
    }
    xhr.send()

    // 偷懒写法
    xhr.onload = function(){
        console.log('请求成功')
    }
    xhr.onloadend = function(){
        console.log('请求完成')
    }
    ```
    
5. 手写 MVVM
    
6. 考 this

    1. fn()

        this => window/global

    2. obj.fn()

        this => obj
    
    3. fn.call(xxx)

        this => xxx
    
    4. fn.apply(xxx)

        this => xxx
    
    5. fn.bind(xxx)

        this => xxx

    6. new Fn()

        this => 新的对象
    
    7. fn 是箭头函数

        this => 外面的 this

7. 闭包和立即执行函数

    i. 闭包 closure

        ```
        function add(){
            var n = 0
            return function(){
                n += 1
            }
        }

        add() // n === 1
        add() // n === 2
        console.log(n) // n is not defined
        ```

    闭包：隐藏变量，保留作用域 => 闭包造成内存泄漏怎么破 => 闭包多返回一个新的函数把引用干掉 => 垃圾回收机制

    内存泄漏：被遗忘的全局变量、未移除的事件监听、定时器和interval等都可能造成内存泄漏。

    ii. 立即执行函数  

        ```
        ;(function(){
            var name
        })()

        (function(){
            var name
        })()
        ~function(){
            var name
        }()
        ```

        造出一个新的作用域，防止污染全局变量。

17. js 垃圾回收机制

    1. 标记清除

        a. 定时的从全局window对象开始找
        b. 标记活着的对象
        c. 清除没有标记的对象

    2. 引用计数

        a. 对象有没有其他对象引用它，如果没有其他对象引用它，就会被回收
        b. 不能解决循环引用的问题
        c. 手动设置引用为 null 或者 undefined

9. async/await 

    - 怎么用

        ```
        async getData(){
            const user = await getUser()
            const data = await getData()
            console.log('data => ', data)
        }
        ```

    - 如何捕获异常

        try catch finally

    - 怎么实现的

        promise + generator 的语法糖

10. 事件循环 eventLoop

    async/await, 同步js, setTimeout, promise 执行顺序

    ```
    async function async1(){
    console.log('async1 start')
    await async2();
    console.log('async1 end')
    }

    async function async2(){
    console.log('async2')
    }

    console.log('script start')

    setTimeout(function(){
    console.log('setTimeout')
    }, 0)

    async1()

    new Promise(function(resolve){
    console.log('promise1')
    resolve()
    }).then(function(){
    console.log('promise2')
    })

    console.log('script end')

    // 输出
    // "script start"
    // "async1 start"
    // "async2"
    // "promise1"
    // "script end"
    // "async1 end"
    // "promise2"
    // "setTimeout"

    ```


11. 正则实现 trim()

    `function trim(str){ return str.replace(/^\s+|\s+$/g,'') }`

12. 手写继承

    i. es6 的继承

    ```
    class Father {
        constructor(a, b){
            this.a = a
            this.b = b
        }
    }
    class Child extends Father {
        constructor(a, b, c){
            super(a, b)
            this.c = c
        }
    }
    ```

    ii. es5 的继承

    ```
    function Human(name){
        this.name = name
    }
    Human.prototype.walk = function(){
        console.log('walk..')
    }
    function Man(name, age){
        Human.call(this, name)
        this.age = age
    }
    var f = function(){}
    f.prototype = Human.prototype
    Man.prototype = new f() // 为什么要用new f() 直接 new Human() 不行吗？原型对象上会有多余的 name 属性
    Man.prototype.sex = '男'
    Man.prototype.constructor = Man
    ```
13. 数组去重

    基本类型 set 去重：`[...new Set(array)]`； 

    不用 set，其他方式去重

14. 手写科里化 sum 函数

    ```
    function sum(){
        var args = [].slice.call(arguments)
        console.log('args ', args)
        var t = args.reduce(function(a,b){
            return a + b
        })
        var v = function(){
            var args2 = [].slice.call(arguments)
            var newArgs = [t].concat(args2)
            return sum.apply(null, newArgs)
        }
        v.valueOf = function(){
            return t
        }
        return v
    }
    sum(1)(2)(3,4)(5).valueOf() // 15
    sum(1,2,3)(4,5).valueOf() // 15
    ```

15. 正则表达式提取 URL 参数

    ```
    var url = 'http://www.sodacar.com/vehicles?car=13242&station=83212jde&lat=102.21301&lng=31.22345'

    var matchs = url.match(/[^&?]*?=[^&?]*/g)

    // matchs = ["car=13242", "station=83212jde", "lat=102.21301", "lng=31.22345"]

    ```

16. setInterval 和 setTimeout

    1. 用 setTimeout 模仿一个 setInterval

    2. setTimeout 的定时器是精准的吗？
        
        不是。js 单线程机制和 eventLoop taskqueue

18. 数组的相关方法

    1. .forEach 可以 return 吗 
    
    2. .slice 会对原来数组进行修改吗？会 
    
    3. .concat 会对原来数组进行修改吗？不会，返回新的数组 
    
    4. .some 和 .every 

    5. es6 数组新增了哪些方法

19. Object.keys V.S. for in 

    得到的是对象的哪些属性？ (自己的，可枚举的)

20. 深拷贝和浅拷贝

    浅拷贝：对内存地址的拷贝

    深拷贝：拷贝对象的引用，以及对象的属性的引用

        1. 判断类型，基本类型和引用类型

        2. 递归拷贝

        3. 检查循环引用（环）

21. 函数提升和变量提升

22. 算法

    - 排序算法（冒泡、快排、选择、堆排、插入排序）

        ```
        
        // 冒泡排序
        var arr = [5,2,3,8,1,9]
        for(let i = 1; i < arr.length; i += 1){
            for(let j = 0; j < array.length - i; j += 1){
                if(arr[j + 1] < arr[j]) {
                    let tmp = arr[j]
                    arr[j] = arr[j+1]
                    arr[j+1] = tmp
                }
            }
        }

        // 快排
        

        // 选择排序
        for(let i = 0; i < arr.length - 1; i += 1){
            let minIndex = i;
            for(let j = i + 1; j < arr.length; j += 1){
                if(arr[j] < arr[minIndex]) {
                    minIndex = j
                }
            }
            if(minIndex !== i) {
                let tmp = arr[i]
                let arr[i] = arr[minIndex]
                arr[minIndex] = tmp
            }
        }

        // 堆排

        // 插入排序
        for(let i = 0; i < arr.length; i += 1){
            let currentIndex = i;
            while(arr[currentIndex - 1] !== undefined && arr[currentIndex] < arr[currentIndex - 1]){
                let tmp = arr[currentIndex - 1]
                arr[currentIndex - 1] = arr[currentIndex]
                arr[currentIndex] = tmp
                currentIndex -= 1
            }
        }
        ```

    - 二叉树

    - 二分查找



