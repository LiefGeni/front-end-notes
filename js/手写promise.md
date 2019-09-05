# 手写一个 promise

一个最简单的 promise 构造函数，接收一个函数并执行，给函数传递一个成功的回调函数，成功之后执行回调函数

```
function Promise(fn){
    var callback = null
    this.then = function(onResolved){
        callback = onResolved
    }
    function resolve(value){
        callback(value)
    }
    fn(resolve)
}
```

上面的代码有问题，因为在 Promise 实例化的时候，callback 此时还是 null，可以用 setTimeout 来解决这个问题

```
function Promise(fn){
    var callback = null
    this.then = function(onResolved){
        callback = onResolved
    }
    function resolve(value){
        setTimeout(function(){
            callback(value)
        }, 0)
        
    }
    fn(resolve)
}
```
试试调用这个 Promise
```
// 没有 .then 绑定 callback，直接 resolve 都会出错
var p = new Promise(function(cb){
    cb(1) // callback 此时还是 null
})

var p = new Promise(function(cb){
    setTimeout(function(){
     cb(2) callback 此时还是 null
    }, 0)
})
// 先生成 promise 实例，然后 .then 绑定回调函数
function getUserId(){
    return new Promise(function(cb){
        setTimeout(function(){
            cb(3)
        }, 0)
    })
}
function showUserId(id){
    console.log(id)
}
getUserId().then(showUserId)
```
给 Promise 实例添加多个回调
```
function Promise(fn){
    var callbacks = []
    this.then = function(onResolved){
        callbacks.push(onResolved)
    }
    function resolve(value){
        setTimeout(function(){
            callbacks.forEach(function(callback){
                callback(value)
            })
        }, 0)
        
    }
    fn(resolve)
}
```
试试多个回调
```
function showUserIdAgain(id){
    console.log('Again' + id)
}
getUserId().then(showUserId)
getUserId().then(showUserIdAgain)
```
一般情况下我们会同步调用 .then 方法的时候，但是有的时候会异步调用 .then 方法。比如买泡面和烧开水，先去买泡面，买好回家水已经烧开了，这时再吃泡面。
```
function boilWater(){ // 烧开水
    return new Promise(function(resolve){
        setTimeout(function(){
            resolve('开水')
        }, 1000)
    })
}

function buyNoodle(){ // 买泡面
    return new Promise(function(resolve){
        setTimeout(function(){
            resolve('泡面')
        }, 2000)
    })
}
 var promiseOfWater = boilWater()

 buyNoodle.then(function(noodle){
     promiseOfWater.then(function(water){
         console.log(noodel + '买好了' + water + '也烧开了')
     })
 })

```
要让 promise 在调用 resolve 之后，还能执行通过 then 添加的回调方法，需要用一个标记区分 promise 状态，如果 promise 还是 pending 状态，那么把 callback 放进回调数组，如果 promise 已经是 fullfilled 状态，直接调用 callback 方法

```
function Promise(fn){
    var callbacks = []
    var state = 'pending'
    var value = null
    this.then = function(onResolved){
        if(state === 'pending'){
            callbacks.push(onResolved)
        }else{
            onResolved(value)
        }
        return this
        
    }
    function resolve(newValue){
        value = newValue
        state = 'fullfilled'
        setTimeout(function(){
            callbacks.forEach(function(callback){
                callback(value)
            })
        }, 0)
        
    }
    fn(resolve)
}
```
实现 .then 的链式调用，假设吃泡面是这样的
```
var promiseOfWater = boilWater()

boilWater().then(makeNoodle).then(eat)
```
.then 要返回 promise 实例，这个promise 实例要做的事情是：

1. 在上一个 promise 执行 resolve 之后，先调用 onResolved 回调；

2. 把得到的结果作为参数放到自身的 then 回调中执行

```
function Promise(fn){
    var handlers = []
    var state = 'pending'
    var value = null
    
    this.then = function(onResolved){
        return new Promise(function(resolve){
            handle({
                onResolved,
                resolve,
            })
        })
    }
    function handle(handler){
        if(state === 'pending'){
            handlers.push(handler)
        }else{
            if(handler.onResolved){
                var res = handler.onResolved(value)
                handler.resolve(res)
            }else{
                handler.resolve(value)
            }
        }
    }
    function resolve(newValue){
        value = newValue
        state = 'fullfilled'
        setTimeout(function(){
            handlers.forEach(function(handler){
                handle(handler)
            })
        }, 0)
    }
    fn(resolve)
}
```
在 promise 构造函数的 resolve 方法里要判断参数是不是 promise 实例，如果是的话，就先调用实例的 then 方法

```
function Promise(fn){
    var handlers = []
    var state = 'pending'
    var value = null
    
    this.then = function(onResolved){
        return new Promise(function(resolve){ // 返回 promise 实例，方便链式调用
            handle({
                onResolved,
                resolve,
            })
        })
    }
    function handle(handler){
        if(state === 'pending'){
            handlers.push(handler)
        }else{
            if(handler.onResolved){
                var res = handler.onResolved(value)
                handler.resolve(res)
            }else{
                handler.resolve(value)
            }
        }
    }
    // 如果 newValue 是 promise 实例，直接调用 .then 方法
    function resolve(newValue){
        if(typeof newValue === 'object' && typeof newValue.then === 'function'){
            // resolve 就是当前这个定义函数
            newValue.then(resolve)
        }else{
            value = newValue
            state = 'fullfilled'
            setTimeout(function(){
                handlers.forEach(function(handler){
                    handle(handler)
                })
            }, 0)
        }
        
    }
    fn(resolve)
}
```

处理 reject 的情况

```
function Promise(fn){
    var handlers = []
    var state = 'pending'
    var value = null
    
    this.then = function(onResolved, onRejected){
        return new Promise(function(resolve, reject){ // 返回 promise 实例，方便链式调用
            handle({
                onResolved,
                resolve,
                onRejected,
                reject,
            })
        })
    }
    function handle(handler){
        if(state === 'pending'){
            handlers.push(handler)
        }else if(state === 'fullfilled'){
            if(handler.onResolved){
                var res = handler.onResolved(value)
                handler.resolve(res)
            }else{
                handler.resolve(value)
            }
        }else{
            if(handler.onRejected){
                var res = handler.onRejected(value)
                // 因为前一个 promise 中有处理 reject 的情况
                // 所以直接调用下一个 promise 的 resolve
                handler.resolve(res)
            }else{
                handler.reject(value)
            }
        }
    }
    // 如果 newValue 是 promise 实例，直接调用 .then 方法
    function resolve(newValue){
        if(typeof newValue === 'object' && typeof newValue.then === 'function'){
            // resolve 就是当前这个定义函数
            newValue.then(resolve, reject)
        }else{
            value = newValue
            state = 'fullfilled'
            setTimeout(function(){
                handlers.forEach(function(handler){
                    handle(handler)
                })
            }, 0)
        }
        
    }
    function reject(reason){
        value = reason
        state = 'rejected'
        setTimeout(function(){
            handlers.forEach(function(handler){
                handle(handler)
            })
        }, 0)
    }
    fn(resolve, reject)
}
```
最后再处理异常的情况

```
function Promise(fn){
    var handlers = []
    var state = 'pending'
    var value = null
    
    this.then = function(onResolved, onRejected){
        return new Promise(function(resolve, reject){ // 返回 promise 实例，方便链式调用
            handle({
                onResolved,
                resolve,
                onRejected,
                reject,
            })
        })
    }
    function handle(handler){
        if(state === 'pending'){
            handlers.push(handler)
        }else if(state === 'fullfilled'){
            if(handler.onResolved){
                try {
                    var res = handler.onResolved(value)
                    handler.resolve(res)
                } catch(e) {
                    handler.reject(e)
                }
                
            }else{
                handler.resolve(value)
            }
        }else{
            if(handler.onRejected){
                try {
                    var res = handler.onRejected(value)
                    // 因为前一个 promise 中有处理 reject 的情况
                    // 所以直接调用下一个 promise 的 resolve
                    handler.resolve(res)
                } catch(e) {
                    handler.reject(e)
                }
            }else{
                handler.reject(value)
            }
        }
    }
    // 如果 newValue 是 promise 实例，直接调用 .then 方法
    function resolve(newValue){
        try {
            if (typeof newValue === 'object' && typeof newValue.then === 'function'){
                // resolve 就是当前这个定义函数
                newValue.then(resolve, reject)
            } else {
                value = newValue
                state = 'fullfilled'
                setTimeout(function(){
                    handlers.forEach(function(handler){
                        handle(handler)
                    })
                }, 0)
            }
        } catch(e) {
            reject(e)
        }
    }
    function reject(reason){
        value = reason
        state = 'rejected'
        setTimeout(function(){
            handlers.forEach(function(handler){
                handle(handler)
            })
        }, 0)
    }
    fn(resolve, reject)
}
```