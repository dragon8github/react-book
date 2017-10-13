### react-redux 的作用

react-redux 提供了集成 Redux 中间件的新姿势。

使用 mapStateToProps 和 mapDispatchToProps 将 Redux 的state 和 dispath 放入 组件的props中。

这样做的好处是，不需要在constructor中去super继承。

另一个好处是，state是自动更新的，而也无需订阅state的更新然后手动更新UI。

---

安装插件

```
$  yarn add react-redux
```

main.js

```js
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore, applyMiddleware } from 'redux'
import { Provider, connect } from 'react-redux'
import TestReduce from './redux/TestReduce'

let store = createStore(TestReduce)

function mapStateToProps (state) {
    return {
        getCount: state.count
    }
}

function mapDispatchToProps (dispatch) {
    return {
        addInput: () => dispatch({ type: 'ADD' })
    }
}

class Test extends React.Component {
    render () {
        const { getCount, addInput } = this.props

        let listInput = () => {
            let result = []
            for (let i = 1; i <= getCount; i++) {
               result.push(<div key = { i }>输入框: <input type='text'/></div>)
            }
            return result
        }

        return <div>
            { listInput() }
            <input type = 'button' onClick = { addInput }  value = '添加'/>
        </div>
    }
}

const App = connect(mapStateToProps, mapDispatchToProps)(Test)

ReactDOM.render(
   <Provider store = { store }>
       <App/>
   </Provider>,
   document.getElementById('root')
)
```

添加 redux/TestReduce.js

```js
export default (state = { count: 1 }, action) => {
    switch (action.type) {
        case 'ADD':
            let a = state.count
            return Object.assign({}, state, { count: a + 1 })
        default:
            return state
    }
}
```

![](/assets/fucksmzqoaaaasasf.png)

