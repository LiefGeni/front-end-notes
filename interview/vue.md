# VUE 面试题

1. 必考：watch 和 computed 区别是什么？

    watch: 观察动作，只要 data 变化就会重新渲染，没有缓存
    computed: 计算属性，有缓存

2. 必考：Vue 有哪些生命周期钩子函数？分别有什么用？

    > 翻译生命周期函数英文就可以了...

    0. beforeCreate

    1. created

    2. beforeMount

    2. mounted

    3. beforeUpdate

    3. updated

    3. beforeDestory

    4. destoryed

3. 必考：Vue 如何实现组件间通信？

    1. 父子组件

        a. `$emit('xxx', data)` 和 `$on('xxx', (data)=>{})`

        b. `$parent` 父链

        c. `$refs` ref 属性
    
    2. 爷孙组件、兄弟组件

        a. var bus = new Vue() // event bus 中央事件总线

    3. vuex: 一个专为 vuejs 开发的状态管理工具

4. 必考：Vue 数据响应式怎么做到的？（以前是 Vue 的双向绑定是怎么实现的...）

    1. v-model

    2. 双向绑定/数据响应式的原理（源代码)

        1. defineProperty getter setter

        2. Vue.set

        3. 或者 this.$set()

5. 必考：Vue.set 是做什么用的？

6. Vuex 你怎么用的？

    1. state

    2. getter

    3. mutation

    4. action

    5. module (用的比较少)

7. VueRouter 你怎么用的？

    1. 重定向和别名

    2. history 模式

    3. 导航守卫(路由守卫)

    3. 懒加载


