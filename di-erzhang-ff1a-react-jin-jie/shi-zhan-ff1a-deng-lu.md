新增 core/Passport.js

```js
export default class Passport {
     constructor () {
         // 是否登录
         this.islogin = false
     }
     ulogin (uname, upass, callback) {
         // 模拟登录成功
         if( uname === "shenyi" && upass === "123") {
            this.islogin = true
            callback()
         } else {
            alert("登录失败")
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
        this.state = {
            userName : '',
            userPass : ''
        }
    }
    setUserInfo (event, key) {
        this.setState({
            [key] : event.target.value
        })
    }
    render () {
        return <div>
            <h3>这是登录界面</h3>
            <div>
                <span>用户名</span>
                <input type = 'text' onChange = {event => {
                    this.setUserInfo(event, 'userName')
                }}/>
            </div>

            <div>
                <span>密码</span>
                <input type = 'password' onChange = {event => {
                    this.setUserInfo(event, 'userPass')
                }}/>
            </div>

            <div>
                <button onClick = {() => {
                    let p = this.props.passport == null ? new Passport() : this.props.passport
                    p.ulogin(this.state.userName, this.state.userPass, () => {
                        this.props.history.push('/news')
                    })}
                }>
                    登录
                </button>
            </div>
        </div>
    }
}
```



---

### 知识点：

1、页面跳转   this.props.history.push\('/news'\)

2、对象的键如果是变量的时候，加入\[\]可以解析。这是ES2015的新特性

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



