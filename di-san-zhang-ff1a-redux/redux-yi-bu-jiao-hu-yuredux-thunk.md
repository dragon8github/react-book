添加 Redux 中间件，用于拦截action

```
$ yarn add redux-thunk
```

本节课使用 redux 来实现点赞功能。环境和代码大部分继承于 [《实战：点赞》](https://dragon8github.gitbooks.io/react/content/di-yi-zhang-ff1a-kai-shi/shi-zhan-ff1a-dian-zan.html)，请先记得开启 redis 和 phpstudy

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
> 异步流程：

3、整个redux的基本流程、使用步骤、基本套路

> 一、定义一个reduce.js文件，在这个文件中我们的目的有两个
>
> 1、定义、初始化状态； 2、定义各种各样的action方法，用于更新状态、获取状态
>
> 二、以reduce为基础，创建一个store.
>
> 我们可以获取store中的状态，也可以调用store中的action方法。  
> 这个store就是我们与状态交互的中间人。  
> 在这个步骤可以加载redux中间件。
>
> 三、监听store.state的变化，并且绑定我们的逻辑代码。这样一来，一旦state发生变化。就会触发我们的逻辑代码。
>
> 譬如在步骤二中，我们将store.state绑定在我们的控件中。一旦state发生变化。我们就需要重新渲染UI。重新绑定数据。这样一来，就产生所谓的双向数据绑定了。
>
> 四、正如步骤二所说，我们可以通过store.dispatch调用store中的action方法来更新状态或者获取状态。



