新增新闻详情页面 components/InfoDetail.js

```js
import React from 'react';

export default class InfoDetail extends React.Component
{
    render()
    {
        return <div>
            <h2>这是我们的新闻详细页</h2>
            <div>
                获取到的参数ID是:{this.props.match.params.newsid}
            </div>
        </div>
    }
}
```

修改NewsTopList.js的代码，加入 Link 路由组件 以及 动态跳转的链接

```js
import React from 'react'
import axios from 'axios'
import { Link } from 'react-router-dom'

export default class NewsTopList extends React.Component {
    constructor (props) {
        super(props)
        this.state = {
            dataList: []
        }
    }
    componentWillMount () {
        axios.get('http://localhost:8080/toplist.php?type=news').then(res => {
            this.setState({
                dataList: res.data
            })
        })
    }
    render () {
        return <dl>
            <dt>新闻列表</dt>
            {
                this.state.dataList.map((item, index) => {
                    return <dd key = {index}>
                        <Link to = {'/news/' + item.newsid}>{ item.title }</Link>
                        <article>{ item.desc }</article>
                    </dd>
                })
            }

        </dl>
    }
}
```

修改路由组件MyRouter.js，新增路由规则

```js
import React from 'react'
import { BrowserRouter as Router, Route, Link } from 'react-router-dom'
import ProductListToplist from './components/ProductTopList'
import NewsTopList from './components/NewsTopList'
import InfoDetail from './components/InfoDetail'

export default class MyRouter extends React.Component {
    render () {
        return  <Router>
            <div>
                <ul>
                    <li><Link to = '/'        >首页      </Link></li>
                    <li><Link to = '/products'>商品排行榜</Link></li>
                    <li><Link to = '/news'    >新闻排行榜</Link></li>
                </ul>

                <Route exact path = '/'             component = { ProductListToplist }/>
                <Route       path = '/products'     component = { ProductListToplist }/>
                <Route exact path = '/news'         component = { NewsTopList }/>
                <Route       path = '/news/:newsid' component = { InfoDetail }/>
            </div>
        </Router>
    }
}
```

![](/assets/luyouRoutersaasdasd.png)

---

### 知识点：

> 1、exact 的作用是什么？

表示精确匹配，以 /news 这个路由为例，如果不加入精确匹配。那么当我们进入 [http://localhost:9000/news/101](http://localhost:9000/news/101) 页面的时候，

不仅加载 InfoDetail 组件， 也会加载 NewsTopList 组件。这是因为两者都被匹配到了。解决方法就是加入 exact 关键词完全精确匹配

这里要清楚一个概念：路由匹配并非加载某个页面，而是加载匹配到的组件。

