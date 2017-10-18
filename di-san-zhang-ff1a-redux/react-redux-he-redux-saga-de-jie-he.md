新建测试用的后端php代码，我们使用了redis来代替数据库操作

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

if (isset($_GET["review"]) && trim($_GET["review"]) != "") {
    $result = sendReview($_GET["review"]);
} else {
    $result = $redis->lrange("newsreview", 0, 10);
}

exit(json_encode($result));
```

main.js

```js
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore,applyMiddleware } from 'redux'
import { Provider,connect} from 'react-redux'
import createSaga from 'redux-saga'  
import axios from 'axios'
import { ReviewReduce } from './redux/ReviewReduce'
import { Review_saga_load, Review_saga_post } from './redux/ReviewSaga'

let saga  = createSaga()
let store = createStore(ReviewReduce, applyMiddleware(saga))
saga.run(Review_saga_load)
saga.run(Review_saga_post)

function mapStateToProps (state) {
    return {
        reviewList: state.rlist
    }
}

function mapDispatchToProps (dispatch) { 
    return{
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

ReactDOM.render(
    <Provider store = { store }>
        <App/>
    </Provider>,    
    document.getElementById('root')
)
```

/redux/NewsReduce.js

```js
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

/redux/ReviewSaga.js

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
        const action= yield take('REVIEW_POST')
        // ajax：提交评论
        yield call(function* () {
            const { content } = yield select()
            const result      = yield call(ReviewAPI.postReview, content)
            yield put({ type: 'REVIEW_LOAD_SUCCESS', reviewdata: result })
        })
    }
}
```

![](/assets/asdasdasjisjiadjiasdjidsajizxcimport.png)

