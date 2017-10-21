传送门

> &lt;bundle-loader&gt;
>
> [https://github.com/webpack-contrib/bundle-loader](https://github.com/webpack-contrib/bundle-loader)

安装

```
yarn add bundle-loader --save
```

components/c1.js 的代码

```js
import React from 'react'
import ReactDOM from 'react-dom'

alert("我是测试组件1号")

class C1 extends  React.Component {
    render () {
        return <div>
            <h2>我是测试组件1号</h2>
        </div>
    }
}

export default C1
```

components/c2.js 的代码

```js
import React from 'react'
import ReactDOM from 'react-dom'

alert("我是测试组件2号")

class C1 extends  React.Component {
    render () {
        return <div>
            <h2>我是测试组件2号</h2>
        </div>
    }
}

export default C1
```

main.js

```js
import React from 'react'
import ReactDOM from 'react-dom'
import {HashRouter as Router, Route, Link } from 'react-router-dom'
import C1 from './components/c1'
import C2 from 'bundle-loader?lazy!./components/c2'

// 异步组件Bundle
class Bundle extends React.Component {
    constructor () {
       super()
       this.state = {
            fuck: null
        }
    }
    componentWillMount () {
        C2(result => this.setState({ fuck: result.default }))
    }
    render () {
        if (this.state.fuck != null) return <this.state.fuck />
        else return null
    }
}

// 异步组件Bundle
const C2_Sync = () => <Bundle load = { C2 }> </Bundle>

// 函数式组件定义
const C3  = () => <div><h2>我是3号组件</h2></div>

ReactDOM.render(
    <Router>
        <div>
            <ul>
                <li><Link to = '/'>   C1组件 </Link></li>
                <li><Link to = '/c2'> C2组件 </Link></li>
                <li><Link to = '/c3'> 测试   </Link></li>
            </ul>
            <Route exact path = '/'   component = { C1 }      />
            <Route       path = '/c2' component = { C2_Sync } />
            <Route       path = '/c3' component = { C3 }      />
        </div>
    </Router>,
    document.getElementById('root')
)
```

![](/assets/啊实打实大三的567uythgfimport.png)

我们发现，默认只弹出“我是测试组件1号”，只有点击了C2组件，才会弹出“我是测试组件2号”

这说明了组件是在我们点击路由才加载的，也就是异步加载的。

