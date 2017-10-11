传送门

> &lt;Redux官网&gt;：[http://redux.js.org/](http://redux.js.org/)
>
> &lt;Redux github&gt;：[https://github.com/reactjs/redux](https://github.com/reactjs/redux)
>
> &lt;Redux-thunk&gt;：https://github.com/gaearon/redux-thunk

Redux 和 React 本身没有任何关系，任何框架甚至不使用框架也可以使用。

```
$ yarn add redux redux-thunk
```

redux/InfoReduce.js

```js
let info = {
    title: '测试新闻标题',
    clicknum: 0
}

export default (state = info, action) => {
    if (action.type === 'INFO_ADDCLCIK') {
        let oldNum = state.clicknum
        oldNum++
        return Object.assign({}, state, { clicknum : oldNum })
    }
    return state
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
        this.state = {
            infoData : store.getState()
        }
    }
    addClick () {
         store.dispatch({
            type : 'INFO_ADDCLCIK'
         })
         this.setState({
            infoData : store.getState()
         })
    }
   render () {
        return <div>
             <h2>新闻标题: { this.state.infoData.title }</h2>
             <span>点击量: { this.state.infoData.clicknum }</span>
             <button onClick = { this.addClick.bind(this) }>修改点击量</button>
        </div>
   }
}

ReactDOM.render(
    <InfoDetail/>, 
    document.getElementById('root')
)
```



