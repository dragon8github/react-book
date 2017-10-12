添加 Redux 中间件，用于拦截action

```
$ yarn add redux-thunk
```

本节课使用 redux 来实现点赞功能。环境和代码大部分继承于 [《实战：点赞》](https://dragon8github.gitbooks.io/react/content/di-yi-zhang-ff1a-kai-shi/shi-zhan-ff1a-dian-zan.html)，请先记得开启redis和phpstudy。

---

修改 main.js ，配置 redux-thunk 中间件

```js
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
import NewsReduce from './redux/NewsReduce'
import { getAgree, setAgree } from './redux/actions'

let store = createStore(NewsReduce, applyMiddleware(thunk))

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
             <span>当前点赞数: { this.S.getState().agreeNum }</span>
             <button onClick = { () => this.S.dispatch(getAgree(101)) }> 获取当前点赞数 </button>
             <button onClick = { () => this.S.dispatch(setAgree(101)) }> 点赞 </button>
        </div>
   }
}

ReactDOM.render(
    <InfoDetail Store = {store}/>, 
    document.getElementById('root')
)
```

添加 redux/actions.js

```js
import axios from 'axios'
import qs from 'qs'

export const getAgree = function (id) {
    return function (dispatch, getState) {
        axios.get("http://localhost:8080/news.php", {
            params: {
                newsid: id
            }
        }).then(res => {
            // dispatch({type: 'GET_AGREE', agreeNum: res.data.agree })
            dispatch(NewsAction.getAgree(res.data.agree))
        })
    }
}

export const setAgree = function (id) {
    return function (dispatch, getState) {
        axios.post('http://localhost:8080/news.php', qs.stringify({
            newsid: id
        })).then(res => {
            // dispatch({type: 'SET_AGREE', agreeNum: res.data.agree })
            dispatch(NewsAction.setAgree(res.data.agree))
        })
    }
}

class NewsAction {
    static getAgree (n) {
        return {
            type: 'GET_AGREE',
            agreeNum: n
        }
    }
    static setAgree (n) {
        return {
            type: 'SET_AGREE',
            agreeNum: n
        }
    }
}
```

修改 redux/NewsReduce.js

```js
let newsData = {
    title    : '测试新闻标题',
    agreeNum : 0
}

export default (state = newsData, action) => {
    switch (action.type) {
        case 'GET_AGREE':
            return Object.assign({}, state, { agreeNum : action.agreeNum })
        case 'SET_AGREE':
            return Object.assign({}, state, { agreeNum : action.agreeNum })
        default:
            return state
    }
}
```

![](/assets/51125312124838321823.png)

---

### 知识点：

1、注册 redux 中间件的方式特别简单。

```
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
let store = createStore(NewsReduce, applyMiddleware(thunk))
```

2、整个过程其实比较容易理解。

> 同步流程：
>
> 创建store -&gt; 监听store state变化事件并绑定回调函数 -&gt; 定义各种Action，更新state / 返回state  -&gt;  通过store.dispatch 触发指定的Action，更新state  / 返回 state -&gt; 某个Action被调用，更新state / 返回 state -&gt; 监听到store state发生变化，调用我们绑定的回调函数 -&gt; Finish!!!!
>
> 异步流程：



