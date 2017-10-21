传送门

> &lt;bundle-loader&gt;
>
> https://github.com/webpack-contrib/bundle-loader

安装

```
yarn add bundle-loader --save
```

先新建两个测试组件，c1/c2

components/c1.js 的代码

```js
import React from 'react'
import ReactDOM from 'react-dom'

alert("我是测试组件:1号")

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

alert("我是测试组件:2号")

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

class Bundle extends React.Component {
    constructor () {
       super()
       this.state = {
            mod: null
        }
    }
    componentWillMount() {
        C2(result => {
            this.setState({
                mod: result.default
            })
        })
    }
    render() {
        if (this.state.mod != null) {
            return <this.state.mod />
        }
        else
            return null
    }
}

const C2_New = () => (
    <Bundle load = { C2 }>
    </Bundle>
)

// 函数式组件定义
function C3 () {
    return <div>
        <h2>我是3号组件</h2>
    </div>
}

ReactDOM.render(
    <Router>
        <div>
            <ul>
                <li><Link to = '/'>   C1组件 </Link></li>
                <li><Link to = '/c2'> C2组件 </Link></li>
                <li><Link to = '/c3'> 测试   </Link></li>
            </ul>
            <Route exact path = '/'   component = { C1 }     />
            <Route       path = '/c2' component = { C2_New } />
            <Route       path = '/c3' component = { C3 }     />
        </div>
    </Router>,
    document.getElementById('root')
)
```

![](/assets/啊实打实大三的567uythgfimport.png)

