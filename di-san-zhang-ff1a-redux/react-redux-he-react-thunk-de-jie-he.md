先来创建后端数据，toplist.php

```php
<?php
header('Access-Control-Allow-Origin:*');  
header('Access-Control-Allow-Headers:x-requested-with,content-type'); 

$news =
'[{"newsid": "101", "pubtime": "2016-10-02", "title": "QFix 探索之路 —— 手Q热补丁轻量级方案", "desc": "QFix是手Q团队近期推出的一种新的Android热补丁方案，在不影响App运行时性能（无需插桩去preverify）的前提下有效地规避了"},'
.'{"newsid": "102", "pubtime": "2016-10-01", "title": "大规模排行榜系统实践及挑战", "desc": "如何支持业务接近接入，数万乃至几十万级排行榜自动化申请调度？选择什么样的存储引擎？怎样避免各业务资源抢占? "},'
.'{"newsid": "103", "pubtime": "2016-09-28", "title": "BitBucket Cloud新增Git大文件存储Beta支持", "desc": "Git LFS背后的基本理念是将大型二进制文件存储在并行存储中，而Git库只包含到那些文件的轻量级引用"},'
.'{"newsid": "104", "pubtime": "2016-09-30", "title": "飞天进化，互联网、数据和计算的聚变", "desc": "阿里巴巴技术委员会主席王坚发布的新书《在线》，被外界视作阿里巴巴技术体系总设计师的王坚出版的第一本著作，吸引了众多参会者的兴趣"}
]';

$products =
'[{"prodID": "909", "prodName": "太平鸟黑色印花短袖"},
  {"prodID": "919", "prodName": "LANVERA蛇纹牛皮女包"},
  {"prodID": "918", "prodName": "片仔癀 祛痘控油乳"},
  {"prodID": "929", "prodName": "TineeV领粉色蕾丝裙"},
  {"prodID": "939", "prodName": "尚都比拉V领束腰裙"}
]';

// 获取新闻列表
if (isset($_GET["type"]) && $_GET["type"] == "news") 
    exit($news);

// 默认获取商品列表
exit($products);
```

main.js，由于场景非常简单。大部分内容直接在本页完成。

```js
import React from 'react'
import ReactDOM from 'react-dom'
import thunk from 'redux-thunk'
import axios from 'axios'
import { createStore, applyMiddleware } from 'redux'
import { Provider, connect } from 'react-redux'

let store = createStore(NewsReduce, applyMiddleware(thunk))

function NewsReduce(state = { newslist: [] }, action) {
    switch (action.type) {
        case 'GET_NEWS':
            return Object.assign({}, state, { newslist: action.getNews })
        default:
            return state
    }
}

function NewsThunk() {
    return function (dispatch, state) {
        axios.get('http://localhost:8080/toplist.php?type=news').then(res => dispatch({type: 'GET_NEWS', getNews: res.data}))
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
                    getNewsList.map((item) => {
                        return <li key = { item.newsid }> { item.title } </li>
                    })
                }
            </ul>
        </div>
    }
}

const App = connect(mapStateToProps, mapDispatchToProps)(TestNewsList)

ReactDOM.render(
    <Provider store = { store }>
        <App/>
    </Provider>,    
    document.getElementById('root')
)
```



