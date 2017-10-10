传送门

> &lt;React Router&gt;： [https://reacttraining.com/react-router/](https://reacttraining.com/react-router/)
>
> &lt;React Router github&gt;： [https://github.com/ReactTraining/react-router](https://github.com/ReactTraining/react-router)
>
> &lt;React Router Web文档&gt;： [https://reacttraining.com/react-router/web/guides/philosophy](https://reacttraining.com/react-router/web/example/basic)

添加路由核心库

```js
$ yarn add react-router react-router-dom
```

toplist.php

```php
<?php
header('Access-Control-Allow-Origin:*');  
header('Access-Control-Allow-Headers:x-requested-with,content-type'); 

$news =
'[{"newsid":"101","pubtime":"2016-10-02","title":"QFix 探索之路 —— 手Q热补丁轻量级方案 ","desc":"QFix是手Q团队近期推出的一种新的Android热补丁方案，在不影响App运行时性能（无需插桩去preverify）的前提下有效地规避了","isdeleted":false},'
.'{"newsid":"102","pubtime":"2016-10-01","title":"大规模排行榜系统实践及挑战 ","desc":" 如何支持业务接近接入，数万乃至几十万级排行榜自动化申请调度？选择什么样的存储引擎？怎样避免各业务资源抢占? ","isdeleted":false},'
.'{"newsid":"103","pubtime":"2016-09-28","title":"BitBucket Cloud新增Git大文件存储Beta支持 ","desc":"Git LFS背后的基本理念是将大型二进制文件存储在并行存储中，而Git库只包含到那些文件的轻量级引用","isdeleted":false},'
.'{"newsid":"104","pubtime":"2016-09-30","title":"飞天进化，互联网、数据和计算的聚变 ","desc":"阿里巴巴技术委员会主席王坚发布的新书《在线》，被外界视作阿里巴巴技术体系总设计师的王坚出版的第一本著作，吸引了众多参会者的兴趣","isdeleted":true}
]';

$products =
'[{"prodID":909,"prodName":"太平鸟黑色印花短袖"},
  {"prodID":919,"prodName":"LANVERA蛇纹牛皮女包"},
  {"prodID":918,"prodName":"片仔癀 祛痘控油乳"},
  {"prodID":929,"prodName":"TineeV领粉色蕾丝裙"},
  {"prodID":939,"prodName":"尚都比拉V领束腰裙"}
]';

// 获取新闻列表
if (isset($_GET["type"]) && $_GET["type"] == "news") 
	exit($news);
	
// 默认获取商品列表
exit($products);
```

components/NewsTopList.js

```js
import React from 'react'
import axios from 'axios'

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
                        <a href = "#">{ item.title }</a>
                        <article>{ item.desc }</article>
                    </dd>
                })
            }
        </dl>
    }
}
```

components/ProductTopList.js

```js
import React from 'react'
import axios from 'axios'

export default class ProductTopList extends React.Component {
    constructor (props) {
        super(props)
        this.state = {
            dataList: []
        }
    }
    componentWillMount () {
        axios.get('http://localhost:8080/toplist.php').then(res => {
            this.setState({
                dataList: res.data
            })
        })
    }
    render () {
        return <dl>
            <dt>商品排行榜</dt>
            {
                 this.state.dataList.map((item, index) => { 
                    return <dd key = {index}>
                        <a href = '#'>{ item.prodName }</a>
                    </dd>
                })
            }
        </dl>
    }
}
```

components/MyRouter.js

```js
import React from 'react'
import { BrowserRouter as Router, Route, Link } from 'react-router-dom'
import ProductListToplist from './ProductTopList'
import NewsTopList from './NewsTopList'

export default class MyRouter extends React.Component {
    render () {
        return  <Router>
            <div>
                <ul>
                    <li><Link to = '/'        >首页      </Link></li>
                    <li><Link to = '/products'>商品排行榜</Link></li>
                    <li><Link to = '/news'    >新闻排行榜</Link></li>
                </ul>

                <hr/>

                <Route exact path = '/'         component = { ProductListToplist }/>
                <Route       path = '/products' component = { ProductListToplist }/>
                <Route       path = '/news'     component = { NewsTopList }/>
            </div>
        </Router>
    }
}
```

main.js

```js
import React from 'react'
import ReactDOM from 'react-dom'
import MyRouter from './components/MyRouter'

ReactDOM.render(
    <MyRouter />,
    document.getElementById('root')
)
```

效果图1：

![](/assets/router1.png)

效果图2：

![](/assets/rouoter2.png)

效果图3：

![](/assets/router3.png)

