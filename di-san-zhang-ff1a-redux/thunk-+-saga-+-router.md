传送门

> &lt;react-router-redux&gt;：[https://github.com/ReactTraining/react-router/blob/master/packages/react-router-redux](https://github.com/ReactTraining/react-router/blob/master/packages/react-router-redux)
>
> &lt;history&gt;：[https://github.com/ReactTraining/history](https://github.com/ReactTraining/history)

安装插件

```
$ yarn add react-router-redux history
```

简单的后端redis操作，每次返回10条。

```php
<?php
header('Access-Control-Allow-Origin:*');  
header('Access-Control-Allow-Methods:GET,POST');  
header('Access-Control-Allow-Headers:x-requested-with,content-type'); 

$redis = new Redis();
$redis->connect("127.0.0.1", 6379);
$result = '';

function sendReview($content) {
    global $redis;
    $redis->lpush("newsreview", $content);
    return $redis->lrange("newsreview", 0, 10);
}

if (isset($_GET["review"]) && trim($_GET["review"]) != "") 
    $result = sendReview($_GET["review"]);
else 
    $result = $redis->lrange("newsreview", 0, 10);

exit(json_encode($result));
```

新建 components/NewsReview.js

```js
import React from 'react'
import { connect } from 'react-redux'

function mapStateToProps (state) {
    console.log(state)
    return {
        reviewList: state.ReviewReduce.rlist
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
import { connect } from 'react-redux'
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
        getNewsList: state.NewsReduce.newslist
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

新建 redux/reducers.js

```js
// 新闻
export const NewsReduce = function (state = { newslist : [] }, action)
{
   switch (action.type) {
        case 'GET_NEWS':
            return Object.assign({}, state, { newslist: action.getNews })
        default:
            return state
    }
}

// 评论
export const ReviewReduce = function (state = { content: '', rlist: [] }, action) {
    switch (action.type) {
        // 评论框内容改变
        case 'REVIEW_CONTENT_CHANGE':
            return Object.assign({}, state, { content: action.content  })
        // 加载或重新加载评论成功
        case 'REVIEW_LOAD_SUCCESS':
            return Object.assign({}, state, { rlist: action.reviewdata })
        default:
            return state
    }
}
```

新建 redux/ReviewSaga.js

```js
import { call, put, takeEvery, select, take, fork, cancel, cancelled } from 'redux-saga/effects'
import axios from 'axios'
import qs from 'qs'

class ReviewAPI {
    //第一次加载评论
    static loadReview () {    
       return axios.get('http://localhost:8080/review.php').then(res => res.data)
    }
    //发送评论
    static postReview (content) {
        return axios.get('http://localhost:8080/review.php?review=' + content).then(res => res.data)
    }
}

export const Review_saga_load = function* () {  
  // 定义【加载评论】任务
  const action = yield take('REVIEW_LOAD')
  // ajax：得到评论的数据
  const result = yield call(ReviewAPI.loadReview)
  // dispatch：加载或重新加载评论成功
  yield put({ type: 'REVIEW_LOAD_SUCCESS', reviewdata: result })
}

export const Review_saga_post = function* () {
    while (true) {
        // 定义【提交评论】任务
        const action = yield take('REVIEW_POST')
        // ajax：提交评论
        yield call(function* () {
            const state  = yield select()
            const result = yield call(ReviewAPI.postReview, state.ReviewReduce.content)
            yield put({ type: 'REVIEW_LOAD_SUCCESS', reviewdata: result })
        })
    }
}
```

main.js

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
import thunk from 'redux-thunk'
import createSaga from 'redux-saga'
import { Review_saga_load, Review_saga_post } from './redux/ReviewSaga'

// 第三方插件
import axios from 'axios'

// reducers
import { NewsReduce, ReviewReduce } from './redux/reducers'

// 我的组件
import NewsReview from './components/NewsReview'
import NewsList   from './components/NewsList'

const history = createHistory()
const router_middleware = routerMiddleware(history)
let saga = createSaga();
const store = createStore(
    combineReducers({
        NewsReduce   : NewsReduce,
        ReviewReduce : ReviewReduce,
        router       : routerReducer
    }),
    applyMiddleware(router_middleware, thunk, saga)
)
saga.run(Review_saga_load)
saga.run(Review_saga_post)

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

![](/assets/balalaslalslasldasdas.png)

---

神坑与知识点![](/assets/xxxxxxxxxxxxxxxx.png)一直以为是哪个文件没有export。最后就是没有找出原因。经过一番调试，最后才知道是react-router-redux出现了问题。

默认使用`yarn add react-router-redux`安装的版本是 react-router-redux@4.0.8。需要换成react-router-redux@next 才可以。版本大概是react-router-redux@5.0.0-alpha.8

```
yarn add react-router-redux@next --save
```



