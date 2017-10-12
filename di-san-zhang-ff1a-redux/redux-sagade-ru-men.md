传送门

> &lt;Redux-saga github&gt;：[https://github.com/redux-saga/redux-saga](https://github.com/redux-saga/redux-saga)
>
> &lt;Redux-saga 官网&gt;： [https://redux-saga.js.org/](https://redux-saga.js.org/)

安装插件

```
$ yarn add redux-saga
```

修改main.js， 使redux集成 redux-saga

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
}

export function* NewsSaga () {
    yield takeEvery("SAGA_001", function* (action) {
        // ajax
        let n = yield call(NewsApi.setAgreeAjax, action.newsid)

        // dispatch
        yield put({type: "SET_AGREE", agreeNum: n })
    })
}
```

redux/NewsReduce.js 不变

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

