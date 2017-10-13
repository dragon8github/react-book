安装插件

```
$  yarn add react-redux
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

![](/assets/fucksmzqoaaaasasf.png)

