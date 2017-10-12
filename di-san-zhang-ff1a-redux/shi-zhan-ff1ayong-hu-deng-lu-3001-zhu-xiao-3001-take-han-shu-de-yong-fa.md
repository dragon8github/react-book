新建user.php

```js
<?php
header('Access-Control-Allow-Origin:*');  
header('Access-Control-Allow-Methods:GET,POST');  
header('Access-Control-Allow-Headers:x-requested-with,content-type'); 
sleep(1);
$result          = new stdClass();
$result->message = "error username or password";
$result->status  = "error";
$result->token   = "none";
if (isset($_POST["user_name"]) && isset($_POST["user_pass"])) {
    $get_userName = $_POST["user_name"];
    $get_userPass = $_POST["user_pass"];
    if ($get_userName == "shenyi" && $get_userPass == "123") {
        $result->message = "login success";
        $result->status  = "success";
        $result->token   = "0ab7f9aa-e246-4359-aee9-8b67fd5dc3f3"
    }
}
header("content-type:application/json");
exit(json_encode($result));
?>
```

新建userlevel.php

```js
<?php
header('Access-Control-Allow-Origin:*');  
header('Access-Control-Allow-Methods:GET,POST');  
header('Access-Control-Allow-Headers:x-requested-with,content-type'); 
sleep(1);
$result = new stdClass();
$result->level = "none";
if (isset($_POST["token"]) && $_POST["token"] == "0ab7f9aa-e246-4359-aee9-8b67fd5dc3f3") {
    $result->level = "黄金VIP";
}
header("content-type:application/json");
exit(json_encode($result));
```

新建UserSaga.js

```js
import { call, put, takeEvery, select, take } from 'redux-saga/effects'
import axios from 'axios'
import qs from 'qs'

class UserAPI {
    static userLogin (user_name, user_pass) {
        return axios.post('http://localhost:8080/user.php', qs.stringify({ user_name: user_name, user_pass: user_pass })).then((res) => res.data)
    }
    static getUserLevel (token) {
        return axios.post('http://localhost:8080/userlevel.php', qs.stringify({ token: token })).then((res) => res.data)
    }
}

export function* UserSaga () {
    // 定义任务，等待被调用
    yield take('USER_LOGIN')
    // 按钮不可用
    yield put({type:"ACTIVE_CHANGE", btnDisabled:true});
    // 获取state
    const {userName, userPass} = yield select()
    // ajax登录
    const { token }  = yield call(UserAPI.userLogin, userName, userPass)
    // 代表用户登录成功
    if (token && token != 'none') {
        // 通知修改 isLogin 标识
        yield put({type: 'LOGIN_SUCCESS'})
        // ajax获取用户等级
        const { level } = yield call(UserAPI.getUserLevel, token)
        // 用户等级获取成功
        if (level && level != 'none') {
            yield put({type: 'UPDATE_USERLEVEL', level})
        // 用户等级获取失败
        } else {
            yield put({type: 'UPDATE_USERLEVEL', level: '获取等级失败'})
        }
    // 代表登录失败
    } else {
        yield put({type: 'LOGIN_ERROR'})
    }
}
```

新建UserReduce.js

```js
export default (state = { btnDisabled: false, isLogin: false, mylevel: '' }, action) => {
    switch (action.type) {
        case 'UPDATE_USERFORM':
            return Object.assign({}, state, action.Form)
        case 'ACTIVE_CHANGE':
            return Object.assign({}, state, { btnDisabled: action.btnDisabled })
        case 'LOGIN_SUCCESS':
            return Object.assign({}, state, { btnDisabled: false, islogin: true })
        case 'LOGIN_ERROR':
            return Object.assign({}, state, { btnDisabled: false, isLogin: false })
        case 'LOGIN_OUT':
            return Object.assign({}, state, { btnDisabled: false, isLogin: false })
        case 'UPDATE_USERLEVEL':
            return Object.assign({}, state, { mylevel: action.level })
        default:
            return state
    }
}
```

修改main.js

```js
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore ,applyMiddleware } from 'redux'
import UserReduce from './redux/UserReduce'
import { UserSaga } from './redux/UserSaga'
import createSaga from 'redux-saga'

let saga = createSaga()
let store = createStore(UserReduce,applyMiddleware(saga))
saga.run(UserSaga)

class UserLogin extends React.Component {
    constructor (props) {
        super(props)
        this.S = this.props.Store
    }
    componentWillMount () {
        this.S.subscribe(() => this.forceUpdate())
    }
    textChange (e, key) {
        this.S.dispatch({type: 'UPDATE_USERFORM', Form:{[key]: e.target.value }})
    }
    userSubmit () {
        this.S.dispatch({type: 'USER_LOGIN'})
    }
    render () {
        return <div>
            <h2>用户登录</h2>
            <div><span>用户名：</span><input type = 'text'    onChange = { e => {this.textChange(e, 'userName')} }/></div>
            <div><span>密  码：</span><input type = 'passwod' onChange = { e => {this.textChange(e, 'userPass')} }/></div>
            <div><button disabled = { this.S.getState().btnDisabled } onClick = { this.userSubmit.bind(this) }> 点这里登录 </button></div>
            <div><span>状    态:</span><span> {this.S.getState().islogin ? '已经登录' : '未登录'}</span></div>
            <div><span>用户等级:</span><span> {this.S.getState().islogin ? this.S.getState().mylevel : '没登录无等级'}</span></div>
        </div>
    }
}

ReactDOM.render(
    <UserLogin Store = { store }/>,
    document.getElementById('root')
)
```



