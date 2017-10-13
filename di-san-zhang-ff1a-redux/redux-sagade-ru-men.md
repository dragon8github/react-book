redux-saga 作为 redux-thunk的代替品。它提供了生成器函数的方式来进行异步请求。提供了更为复杂的业务操作

学习曲线也比thunk高很多。但作为热门的Redux中间件。还是必须掌握一下的。

代码依然是基于 [&lt;实战：点赞&gt;](https://dragon8github.gitbooks.io/react/content/di-yi-zhang-ff1a-kai-shi/shi-zhan-ff1a-dian-zan.html)

传送门

> &lt;Redux-saga github&gt;：[https://github.com/redux-saga/redux-saga](https://github.com/redux-saga/redux-saga)
>
> &lt;Redux-saga 官网&gt;： [https://redux-saga.js.org/](https://redux-saga.js.org/)

安装插件

```
$ yarn add redux-saga
```

修改main.js， 使 redux 集成 redux-saga，并对saga进行初始化

```js
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore, applyMiddleware } from 'redux'
import createSaga from 'redux-saga'
import NewsReduce from './redux/NewsReduce'
import { NewsSaga } from './redux/NewsSaga'

let saga = createSaga()
let store = createStore(NewsReduce, applyMiddleware(saga))
saga.run(NewsSaga)

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
             <button onClick = { () => this.S.dispatch({type: "SAGA_001", newsid: 101}) }> 点赞 </button>
             <button onClick = { () => this.S.dispatch({type: "SAGA_002", newsid: 101}) }> 获取当前点赞数 </button>
        </div>
   }
}

ReactDOM.render(
    <InfoDetail Store = {store}/>, 
    document.getElementById('root')
)
```

新建 redux/NewsSaga.js

```js
import { call, put, takeEvery } from 'redux-saga/effects'
import axios from 'axios'
import qs from 'qs'

class NewsApi {
    static setAgreeAjax (newsid) {
        return axios.post('http://localhost:8080/news.php', qs.stringify({ newsid: newsid })).then(res => res.data.agree)
    }
    static getAgreeAjax (newsid) {
        return axios.get("http://localhost:8080/news.php", { params: { newsid: newsid }} ).then(res => res.data.agree)
    }
}

export function* NewsSaga () {
    // SAGA_001
    yield takeEvery("SAGA_001", function* (action) {
        // ajax
        let n = yield call(NewsApi.setAgreeAjax, action.newsid)
        // dispatch
        yield put({type: "SET_AGREE", agreeNum: n })
    })

    // SAGA_002
    yield takeEvery("SAGA_002", function* (action) {
        // ajax
        let n = yield call(NewsApi.getAgreeAjax, action.newsid)
        // dispatch
        yield put({type: "GET_AGREE", agreeNum: n })
    })
}
```

redux/NewsReduce.js 保持不变

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

![](/assets/daiiqjwiwijsadijdassaga.png)

---

### 知识点

1、saga.run\(NewsSaga\) 中必须绑定一个生成器函数

2、takeEvery第一个参数是一个自定义名称，用于被外部dispatch指定。第二个参数必须是一个生成器函数。

