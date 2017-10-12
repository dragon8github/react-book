新建user.php

```php
<?php
header('Access-Control-Allow-Origin:*');  
header('Access-Control-Allow-Methods:GET,POST');  
header('Access-Control-Allow-Headers:x-requested-with,content-type');
sleep(1);
$result = new stdClass();
$result->message = "error username or password";
$result->status = "error";
if (isset($_POST["user_name"]) && isset($_POST["user_pass"])) {

    $get_userName = $_POST["user_name"];
    $get_userPass = $_POST["user_pass"];
    if($get_userName == "shenyi" && $get_userPass == "123") {

        $result->message = "login success";
        $result->status  = "success";
    }
}
header("content-type:application/json");
exit(json_encode($result));     
?>
```

新建 /redux/UserReduce.js

```js
export default (state = { btnDisabled : false }, action) => {
    switch (action.type) {
        case 'UPDATE_USERFORM':
            return Object.assign({}, state, action.Form)
        case 'ACTIVE_CHANGE':
            return Object.assign({}, state, { btnDisabled : action.btnDisabled })
        default:
            return state
    }
}
```

新建 /redux/UserSaga.js

```js
import { call, put, takeEvery, select} from 'redux-saga/effects'
import axios from 'axios'
import qs from 'qs'

class UserAPI {
    static userLogin (user_name, user_pass) {
        return axios.post('http://localhost:8080/user.php', qs.stringify({ user_name: user_name, user_pass : user_pass })).then((res) => res.data)
    }
}

export function* UserSaga () {
   yield takeEvery('USER_LOGIN', function* (action) {
        // 登录时，先使用 dispatch 通知 ACTIVE_CHANGE，关闭按钮
        yield put({type : 'ACTIVE_CHANGE', btnDisabled : true})
        // 获取state
        const getState = yield select()
        // ajax
        const result   = yield call(UserAPI.userLogin, getState.userName, getState.userPass)
        // 查看是否成功
        alert(result.status)
        // 异步完成， 再使用 dispatch 通知 ACTIVE_CHANGE，开启按钮
        yield put({type : 'ACTIVE_CHANGE', btnDisabled : false})
    })
}
```

修改main.js

```js
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore ,applyMiddleware } from 'redux'
import UserReduce from './redux/UserReduce'
import {UserSaga} from './redux/UserSaga'
import createSaga from 'redux-saga'

let saga = createSaga()
let store = createStore(UserReduce,applyMiddleware(saga))
saga.run(UserSaga)

class UserLogin extends React.Component {
    constructor () {
        super()
    }
    componentWillMount () {
        this.props.Store.subscribe(() => {
            this.forceUpdate()
        })
    }
    textChange (e, key) {
        let updater = {}
        updater[key] = e.target.value
        this.props.Store.dispatch({type : 'UPDATE_USERFORM', Form : updater})
    }
    userSubmit () {
        this.props.Store.dispatch({type : 'USER_LOGIN'})
    }
    render () {
        return <div>
            <h2>用户登录</h2>
            <div>
                <span>用户名</span>
                <input type = 'text' onChange = {(event) => {this.textChange(event, 'userName')}}/>
            </div>
            <div>
                <span>密码</span>
                <input type = 'text' onChange = {(event) => {this.textChange(event, 'userPass')}}/>
            </div>
            <div>
                <button disabled = {this.props.Store.getState().btnDisabled} onClick = {this.userSubmit.bind(this)}>点这里登录</button>
            </div>
        </div>
    }
}

ReactDOM.render(
    <UserLogin Store = { store }/>,
    document.getElementById('root')
)
```

![](/assets/successiajdij12izdhmmm.png)

