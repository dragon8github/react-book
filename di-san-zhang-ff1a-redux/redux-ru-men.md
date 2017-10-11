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

redux/InfoReduce.js

```js
let info = {
    title    : '测试新闻标题',
    clicknum : 0
}

export default (state = info, action) => {
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
import InfoReduce from './redux/InfoReduce'

let store = createStore(InfoReduce)

class InfoDetail extends React.Component {
    constructor () {
        super()
    }
    componentWillMount () {
        this.props.Store.subscribe(() => this.forceUpdate())
    }
   render () {
        return <div>
             <h2>新闻标题: { this.props.Store.getState().title }</h2>
             <span>点击量: { this.props.Store.getState().clicknum }</span>
             <button onClick = { () => this.props.Store.dispatch({ type: 'INCREMENT' }) }> 加一 </button>
             <button onClick = { () => this.props.Store.dispatch({ type: 'DECREMENT' }) }> 减一 </button>
        </div>
   }
}

ReactDOM.render(
    <InfoDetail Store = {store}/>, 
    document.getElementById('root')
)

```



