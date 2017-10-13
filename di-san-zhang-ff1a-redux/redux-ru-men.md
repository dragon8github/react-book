传送门

> &lt;Redux官网&gt;：[http://redux.js.org/](http://redux.js.org/)
>
> &lt;Redux github&gt;：[https://github.com/reactjs/redux](https://github.com/reactjs/redux)
>
> &lt;Redux-thunk&gt;：[https://github.com/gaearon/redux-thunk](https://github.com/gaearon/redux-thunk)

---

### redux 理论

redux是一个全局状态管理器。它提供了一个仓库，供你放置数据、获取数据、修改数据以及监听数据变化。

为了方便学习，你可以先理解为类似localStorage、session、cookie的东西。只是它更加强大更加安全。

Redux 和 React 本身没有任何关系，任何框架甚至不使用框架也可以使用。

它存在的意义在于解决大型组件树之间复杂的数据交互问题。

在reactjs中，组件之间的数据通讯几乎只能依赖于属性（props）的传递。

如果某些数据需要在超过三个以上的组件之间来回传递，就会开始变得复杂而难以维护。

一个好的思路是将这些数据放置在全局之中，各个组件都可以根据自己的需要自由地对这些数据进行获取和更新。

如果某些组件对这些数据的变化感兴趣，还可以订阅数据的更新事件。一旦数据更新，就会通知组件。

一个常见的场景是：控件绑定着redux的状态，并且我们监听数据的变化。当数据进行变化的时候，我们更新UI即可

---

### Reduce是什么？

```js
// 创建一个仓库
let Store = createStore(Reduce)
```

这里的 Reduce 是一个函数，正确来说，它就是一个仓库管理员。它是创建仓库的关键因素，我们来看一段实战代码：

```js
export default (state = {title: 'my title', clicknum: 0 }, action) => {
    switch (action.type) {
        case 'INCREMENT':
            let a = state.clicknum
            return Object.assign({}, state, { clicknum : a + 1, title: action.title })
        case 'DECREMENT':
            let b = state.clicknum
            return Object.assign({}, state, { clicknum : b - 1, title: action.title }) 
        default:
            return state
    }
}
```

这个函数一共做了三件重要的事情：

**1、初始化state**

```
这很重要，因为在我们创建Store的时候，Redux会自动执行一遍该函数，试图根据函数的返回object，作为state的初始值。

在这之后, 最新的state总会传入函数的第一参数中。
```

**2、根据 action.type，定义或执行各种任务**

```
函数的第二参数action，实际是 Store.dispatch\(\) 传入的。如：
```

```js
Store.dispatch('INCREMENT', {title: "foo"});
// 或者
Store.dispatch({type: 'DECREMENT', title: 'bar')};
```

**3、返回并更新State**

    一旦 **return** 的 object，就会自动替换并作为仓库最新的state。并且还会触发state更新的推送。

    所以官方推荐使用 `Object.assgin({}, state, {...}) `的方式合并返回。

### 基本使用

```js
// 获取状态
Store.getState()

// 派生事件
Store.dispatch('type_name', {key: value})

// 订阅状态更新
Store.subscribe(() => {
    // do something...
})
```

---

### redux 实战

> $ yarn add redux

新建 redux/newsReduce.js

```js
export default (state = {title: '测试新闻标题', clicknum: 0 }, action) => {
    switch (action.type) {
        case 'INCREMENT':
            let a = state.clicknum
            return Object.assign({}, state, { clicknum : a + 1 })
        case 'DECREMENT':
            let b = state.clicknum
            return Object.assign({}, state, { clicknum : b - 1 }) 
        default:
            return state
    }
}
```

main.js

```js
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore } from 'redux'
import NewsReduce from './redux/NewsReduce'

let store = createStore(NewsReduce)

class InfoDetail extends React.Component {
    constructor (props) {
        super(props)
        this.S = this.props.Store
    }
    componentWillMount () {
        this.S.subscribe(() => this.forceUpdate())
    }
   render () {
        return <div>
             <h2>新闻标题: { this.S.getState().title }</h2>
             <span>点击量: { this.S.getState().clicknum }</span>
             <button onClick = { () => this.S.dispatch({ type: 'INCREMENT' }) }> 加一 </button>
             <button onClick = { () => this.S.dispatch({ type: 'DECREMENT' }) }> 减一 </button>
        </div>
   }
}

ReactDOM.render(
    <InfoDetail Store = {store}/>,
    document.getElementById('root')
)
```

![](/assets/asdasdasxzcxzqwe213123.png)

---

### 知识点

1、只要有属性传入组件，请养成使用 `constructor (props) { super(props) }`的习惯，这样做的好处是方便在组件中直接使用this.props来调用传入的属性。

2、action 必须返回一个新的对象，常用的做法是使用 Object.assgin\({}, obj1, obj2...\) 重新生产一个对象。

3、在实战中，都是将 Store 作为属性传入组件中使用的`<InfoDetail Store = {store}/>`

4、通过 Store.subscribe\(cb\) 方法订阅 Store 的更新事件。在此 Demo 中，每次 Store 中的 State 更新，我们都使用 forceUpdate\(\) 重新渲染 render\(\)

