新增 core/Passport.js

```js
export default class Passport {
     constructor () {
         this.islogin = false
     }
     ulogin (uname, upass, successCallBack, failCallBack) {
         if (uname === "shenyi" && upass === "123") {
            this.islogin = true
            let successData = {msg: "登录成功", result: "success", resultCode: 1}
            return successCallBack ? successCallBack(successData) : new Promise((resolve, reject) => resolve(successData))
         } else {
            let failData = {msg: "登录失败，密码错误或账号不存在", result: "fail", resultCode: 0}
            return failCallBack ? failCallBack(failData) : new Promise((resolve, reject) => reject(failData))
         }
     }
}
```

新增 components/UserLogin.js

```js
import React from 'react'
import Passport from '../core/Passport'

export default class UserLogin extends React.Component {
    constructor (props) {
        super(props)
    }
    render () {
        return <div>
            <h3>这是登录界面</h3>
            <div>
                <span>用户名</span>
                <input type = 'text' ref = { ele => this.userNameInput = ele }/>
            </div>
            <div>
                <span>密码</span>
                <input type = 'password' ref = { ele => this.userPassInput = ele }/>
            </div>
            <div>
                <button onClick = {() => {
                    let p = this.props.passport == null ? new Passport() : this.props.passport
                    p.ulogin(this.userNameInput.value, this.userPassInput.value).then(data => {
                        this.props.history.push('/news')
                    }).catch(err => {
                        window.alert(err.msg);
                    })
                }}>
                    登录
                </button>
            </div>
        </div>
    }
}
```

修改 MyRouter.js 的代码

```js
import React from 'react'
import { HashRouter as Router, Route, Link, Redirect } from 'react-router-dom'
import ProductListToplist from './components/ProductTopList'
import NewsTopList from './components/NewsTopList'
import InfoDetail from './components/InfoDetail'
import UserLogin from './components/UserLogin'
import Passport from './core/Passport';

let passport = new Passport()

export default class MyRouter extends React.Component {
    render () {
        return  <Router>
            <div>
                <ul>
                    <li><Link to = '/'        >首页      </Link></li>
                    <li><Link to = '/products'>商品排行榜</Link></li>
                    <li><Link to = '/news'    >新闻排行榜</Link></li>
                    <li><Link to = '/login'   >登录      </Link></li>
                </ul>

                <Route exact     path = '/'             component = { ProductListToplist }/>
                <Route           path = '/products'     component = { ProductListToplist }/>
                <Route           path = '/news/:newsid' component = { InfoDetail }/>  
                <Route exact     path = '/news'  render = { props => {
                    return passport.islogin ? <NewsTopList { ...props }/> : <Redirect to = '/login'/>
                }}/> 
                <Route           path = '/login' render = { props => {
                    return <UserLogin passport = { passport } { ...props } />
                }}/>
            </div>
        </Router>
    }
}
```

---

### 知识点：

1、页面跳转   this.props.history.push\('/news'\)

2、object变量赋值的时候，如果键是变量的话，加入\[\]可以解析。这是ES2015的新特性

```js
 // 正常的做法
 let obj = {}
 obj[key] = event.target.value
 this.setState(obj)

 // ES2015的新特性
 this.setState({
    [key] : event.target.value
 })
```



