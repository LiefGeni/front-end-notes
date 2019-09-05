# React 面试题

1. 生命周期
    
    初始化：constructor => WillMount => render => DidMount => WillUnmount

    更新：WillReceiveProps => ShouldUpdate => WillUpdate => render => DidUpdate

    ajax 请求放在 DidMount 生命周期之后

2. setState 做了什么事情

    更新 state，重新渲染页面。

    setState 是异步(在 react 的合成事件和钩子函数中）的，不会立刻改变 state 的值（页面重新 render 之后才会改变 state）

    多次调用 setState 会合并，最后页面渲染可能只有一次

    ```
    // 比如如下 setState，其实 count 的值只会加1
    this.setState({count: this.state.count + 1});
    this.setState({count: this.state.count + 1});
    this.setState({count: this.state.count + 1});
    ```

    如何让 setState 连续更新
    
    1. 函数做参数

        ```
        setState((state, props) => {
            return {} // new state
        })

        this.setState((state, props)=>{
            // 用函数做参数，react 可以保证下次 setState 的时候结果是合并的
            // 但是 this.state 仍然要等到 render 之后再更新
            return { count: state.count + 1 }
        })

        ```

    2. callback 回调

        ```
        setState(state, callback)
        ```

    3. 用其他生命周期钩子替代
        
        1. setState 更新之后的操作放在 componentDidMount
        
        2. 或者 componentDidUpdate 生命周期函数里


    setState 也可以是同步更新的，举个🌰(原生事件和 setTimeout)

    https://codesandbox.io/s/54rpnjlrw4

3. diff 算法

    1. tree diff
        - 比较同层级的节点
    2. component diff
        - 相同类 vs 不同类
    3. element diff 
        - 加 key 的作用

4. container component V.S. UI component

    a. 容器类组件需要维护状态和数据，也是其他UI组件的数据源

    b. UI类组件大多是无状态的，不需要维护数据，即便是有状态的UI状态，维护的也是UI视图的状态数据

    c. UI类组件的数据和操作函数都是从属性拿到的

5. 函数式组件 V.S. class 组件

    a. 函数式组件更多是纯UI组件，没有自己的状态

    b. 函数式组件没有生命周期

5. 受控组件 V.S. 不受控组件

    ```
    <FInput value={x} onChange={fn}></FInput> // 受控组件
    <Input defaultValue={x}></Input> // 不受控组件
    ```

6. 组件间的通信

    a. props 

    b. redux

7. shouldComponentUpdate 和 PureComponent

    a. 浅比较

    ```
    // 这样写的代码，不能发挥 PureButton 的作用，因为每次都生成了一个新的匿名函数对象
    render(){
        return(
            <div>
                <PureButton onClick={()=>{console.log('clicked')}} />
                // ... 省略其他组件
            </div>
        )
    }
    // 应该这样写
    handleClick = () => { console.log('clicked') }
    render(){
        return(
            <div>
                <PureButton onClick={this.handleClick} />
                // ... 省略其他组件
            </div>
        )
    }
    ```
    代码改写之后，即便父组件 state 或者 props 变化导致重新 render，PureButton 组件是不需要重新渲染的，达到了性能优化的目的

8. 虚拟 DOM 

    - js 对象
    - 比较两次更新的不同

9. 高阶组件（HOC)

    a. 接收组件做参数，返回组件的组件

    b. connect() 高阶函数

10. redux

    a. 统一状态管理，解决组件间的数据共享问题

    b. 单向数据流

11. connect

    a. react-redux 提供的方法

    b. 原理：

13. 不可变数据 

14. reselect

    - render 中生成新的对象
        在这种场景中，即便用上 PureComponent 也无法解决问题，需要配合 reselect

        ```
        render(){
            const data = this.props.posts.map(post => { /*...省略无关代码...*/})
            return (
                <FlatList data={data}></FlatList>
            )
        }
        ```
        由于每次在 render 中都生成了新的对象 data，即便 FlatList 是 PureComponent，但是传递的参数对象每次都是新的，因此 FlatList 每次也会重新渲染。
        
        这种情况下，我们需要一个记忆函数，假设每次传入的参数不变，那么返回的 data 对象也不需要变，就是原来的旧的。如果参数变了，再重新计算返回一个新的对象。reselect 就是这个记忆函数。

        看一下 reselect 如何使用，首先创建一个 selector
        ```
        import { createSelector } from 'reselect'

        const getPosts = (state) => state.posts

        const getVisiblePosts = createSelector(
            [getPosts],
            (posts) => {
                return posts.map((post) => { /*...省略无关代码...*/})
            }
        )
        export default getVisiblePosts
        ```
        createSelector 从 getPosts 这个函数中获取参数 posts，然后去记忆下面这个匿名函数。
        ```
        (posts) => {
            return posts.map((post) => { /*... 省略无关代码...*/ })
        }
        ```

        重新改写父组件
        ```
        
        const mapStateToProps = (state) => {
            return {
                posts: getVisiblePosts(state)
            }
        }
        
        render(){
            // 下面这一行代码就不要了
            // const data = this.props.posts.map(post => { /*...省略无关代码...*/})
            // 直接从 props 拿需要的数据
            return (
                <FlatList data={this.props.posts}></FlatList> 
            )
        }
        ```
        代码改写之后，即便父组件的 render 被触发了，但是只要 state.posts 没有更新，那么 this.props.posts 也不会重新计算生成新的，那么 FlatList 就不会重新渲染了。（用了 reselect 之后，即便 FlatList 不是 PureComponent，只要 this.props.posts 没有发生变化，那么就不会引起重新 render)