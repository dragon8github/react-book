传送门

> &lt;react-router-redux&gt;：[https://github.com/ReactTraining/react-router/blob/master/packages/react-router-redux](https://github.com/ReactTraining/react-router/blob/master/packages/react-router-redux)
>
> &lt;history&gt;：[https://github.com/ReactTraining/history](https://github.com/ReactTraining/history)

安装插件

```
$ yarn add react-router-redux history
```

新建 router-index.js

```js
// react
import React from 'react'
import ReactDOM from 'react-dom'

// redux
import { createStore, combineReducers, applyMiddleware } from 'redux'
import { Provider } from 'react-redux'

// router + history
import {Route, Link } from 'react-router-dom'
import { ConnectedRouter, routerReducer, routerMiddleware, push } from 'react-router-redux'
import createHistory from 'history/createHashHistory'

// redux-middleware
import thunk from 'redux-thunk';
import createSaga from 'redux-saga'  
import { call, put, takeEvery, select, take, fork, cancel, cancelled } from 'redux-saga/effects'

// 第三方插件
import axios from 'axios'

// reducers
import { NewsReduce, ReviewReduce } from './redux/reducers'

// 我的组件
import NewsReview from './components/NewsReview'
import NewsList   from './components/NewsList'

const router_middleware = routerMiddleware(createHistory())

const store = createStore(
    combineReducers({
        NewsReduce   : NewsReduce,
        ReviewReduce : ReviewReduce,
        router       : routerReducer
    }),
    applyMiddleware(router_middleware, thunk, saga)
)

ReactDOM.render(
    <Provider store = { store }>
        <ConnectedRouter history = { history }>
            <div>
                <ul>
                    <li><Link to = '/'>       头条新闻 </Link></li>
                    <li><Link to = '/news'>   新闻列表 </Link></li>
                    <li><Link to = '/review'> 新闻评论 </Link></li>
                </ul>
                <Route exact path = '/'       component = { NewsList }/>
                <Route       path = '/news'   component = { NewsList }/>
                <Route       path = '/review' component = { NewsReview }/>
            </div>
        </ConnectedRouter>
    </Provider>,
    document.getElementById('root')
)
```

新建/components/NewsReview.js

```js
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore,applyMiddleware } from 'redux'
import { Provider,connect} from 'react-redux'
import createSaga from 'redux-saga'  

function mapStateToProps (state) {
    return {
        reviewList: state.rlist
    }
}

function mapDispatchToProps (dispatch) { 
    return {
        textChange: e  => dispatch({ type: 'REVIEW_CONTENT_CHANGE', content: e.target.value }),
        loadReview: () => dispatch({ type: 'REVIEW_LOAD' }),
        postReview: () => dispatch({ type: 'REVIEW_POST' })
    }
}

class TestReviewList extends React.Component {
    componentWillMount () {
        const { loadReview } = this.props
        loadReview()
    }
    render () {

        const { reviewList, textChange, postReview } = this.props

        return <div>
            <div className = 'newscontent'>
                <h1>全球的开发新趋势调查</h1>
                <article>在全球著名 IT 技术网站 Stack Overflow 上，我们可以基于该网站的开发者调查数据，了解全球的开发新趋势及动态。 Stack Overflow 分析了其网站上各编程语言的标签的访问情况： 发达国家程序员喜欢 Python、R（重视科研）、C 与 C++（重视教育）； 欠发达国家的喜欢 PHP 与 Android 开发 </article>
            </div>
            <h2>评论区域</h2>
            <dl>
                 <dt>输入评论内容</dt>
                 <dd><textarea className='review' onChange = {e => { textChange(e) }}/></dd>
                 <dd><input className = 'cmd' type = 'button' value = '提交'  onClick = { postReview }/></dd>
            </dl>
            <h3>评论列表区域</h3>
            <ul>
                {
                    reviewList.map((item, index) => {
                        return <li key = { index }> { item } </li>
                    }
                )}
            </ul>
        </div>
    }
}

const App = connect(mapStateToProps, mapDispatchToProps)(TestReviewList)

export default App

```

新建/components/NewsList.js

```js
import React from 'react'
import ReactDOM from 'react-dom'
import thunk from 'redux-thunk'
import { createStore, applyMiddleware } from 'redux'
import { Provider, connect } from 'react-redux'
import { NewsThunk } from './../redux/actions'
import axios from "axios"

// 本来应当放在 actions.js 中独立开来的, 但考虑到函数的数量少就省略了
function NewsThunk() {
   return function (dispatch, state) {
        axios.get('http://localhost:8080/toplist.php?type=news').then(res => dispatch({
            type: 'GET_NEWS', 
            getNews: res.data
        }))
    }
}

function mapStateToProps (state) {
    return {
        getNewsList: state.newslist
    }
}

function mapDispatchToProps (dispatch) {
    return {
        loadNews: () => {
            dispatch(NewsThunk())
        }
    }
}

class TestNewsList extends React.Component {
    componentWillMount () {
        const { loadNews } = this.props
        loadNews()
    }
    render () {
        const { getNewsList } = this.props
        return <div>
            <h2>新闻列表</h2>
            <ul>
                {
                    getNewsList.map(item => {
                        return <li key = { item.newsid }> { item.title } </li>
                    })
                }
            </ul>
        </div>
    }
}

const App = connect(mapStateToProps, mapDispatchToProps)(TestNewsList)

export default App
```



