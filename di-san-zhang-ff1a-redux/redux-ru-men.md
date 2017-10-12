传送门

> &lt;Redux官网&gt;：[http://redux.js.org/](http://redux.js.org/)
>
> &lt;Redux github&gt;：[https://github.com/reactjs/redux](https://github.com/reactjs/redux)
>
> &lt;Redux-thunk&gt;：[https://github.com/gaearon/redux-thunk](https://github.com/gaearon/redux-thunk)

Redux 和 React 本身没有任何关系，任何框架甚至不使用框架也可以使用。

```
$ yarn add redux
```

redux/newsReduce.js

```js
let newsData = {
    title    : '测试新闻标题',
    clicknum : 0
}

export default (state = newsData, action) => {
    switch (action.type) {
        case 'INCREMENT':
            return Object.assign({}, state, { clicknum : state.clicknum + 1 })
        case 'DECREMENT':
            return Object.assign({}, state, { clicknum : state.clicknum - 1 }) 
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

2、dispatch action 必须返回一个新的对象，常用的做法是使用object.assgin\({}, obj1, obj2...\)

3、在实战中，都是将 Store 作为属性传入组件中使用的`<InfoDetail Store = {store}/>`

4、通过 Store.subscribe\(cb\) 方法订阅 Store 的更新事件。在此 Demo 中，每次更新我们都使用 forceUpdate\(\) 重新渲染 render\(\) .它和 setState\(\) 功能十分相似。值得一提的是，由于是订阅，只要Store一旦更新，它就会持续的触发我们绑定的函数。是一种会持续且不断触发的事件。

