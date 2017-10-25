新建userauth.php，模拟权限分配

```php
<?php
header('Access-Control-Allow-Origin:*');  
header('Access-Control-Allow-Methods:GET,POST');  
header('Access-Control-Allow-Headers:x-requested-with,content-type'); 

sleep(2);

class user {
    // 用户ID写死为1001
    public $user_id = 1001;     
    // 权限列表, 必须初始化为一个空数组, 否则前端会出错
    public $permissions = []; 
    // 构造函数
    public function __construct($uid) {
        $this->user_id = $uid;
    }
}

// 一个无比简单的权限类
class permission {
   public $url;
   public function __construct($url){
        $this->url = $url;
   }
}

$user = new User(1001);
$per1 = new permission('/user/add');
$user->permissions[] = $per1; 

header('Content-type:application/json');
exit(json_encode($user));
```

---

> Api系列

新建Api/ApiConfig.js

```js
export const apiHost = 'http://localhost:8080'

export default {
    menu: '/Menu.php',
    auth: '/userauth.php'
}
```

新建Api/AuthApi.js

```js
import axios from 'axios'
import config, { apiHost } from './ApiConfig'

export default class AuthApi {
    static getAuthData () {
       return axios.get(apiHost + config.auth).then(result => result.data)
    }
}
```

新建Api/MenuApi.js

```js
import axios from 'axios'
import config, { apiHost } from './ApiConfig'

export default class MenuApi {
    static getMenuData () {
       return axios.get(apiHost + config.menu).then(result => result.data)
    }
}
```

---

> Components系列

新建Components/Hoc.js，专门生产高阶函数

```js
import React from 'react'

function MessageHoc (msg) {
    const props = { message : msg }
    return function (Abc) {
        return class extends React.Component {
            render () {
                return <Abc {...props} />
            }
        }
    }
}

export { 
    MessageHoc
}
```

新建Components/Error.js，专门用来跳转没有权限的页面

```js
import React from 'react'

class BaseError extends React.Component {
    render () {
        const { message } = this.props
        return <div>
            <h3> { message } </h3>
        </div>
    }
}

export { BaseError }
```

添加Component/user/addUser.js

```js
import React from 'react'
import { connect } from 'react-redux'

class AddUser extends React.Component {
    render () {
        return <div>
            <h3>新增用户</h3>
        </div>
    }
}

export default connect()(AddUser)
```

添加Component/user/listUser.js

```js
import React from 'react'
import { connect } from 'react-redux'

class ListUser extends React.Component {
    render () {
        return <div>
            <h3>用户列表</h3>
        </div>
    }
}

export default connect()(ListUser)
```

修改Component/layout/top\_sider\_nav.js

```js
import React from 'react'
import { Layout, Menu, Breadcrumb, Icon, Spin  } from 'antd'
import { HashRouter as Router, Route, Link, NavLink } from 'react-router-dom'
import { connectedRouterRedirect } from 'redux-auth-wrapper/history4/redirect'
import { withRouter } from 'react-router'
import { connect }    from 'react-redux'
import { BaseError }  from "@Components/Error"
import { MessageHoc } from "@Components/Hoc"
import AddUser  from "@Components/user/addUser"
import ListUser from "@Components/user/listUser"

const { SubMenu } = Menu
const { Header, Content, Sider } = Layout

const userIsAuthenticated = connectedRouterRedirect({
    redirectPath: '/error',
    authenticatedSelector: state => {
        // console.log(state)
        // 获取当前url 
        var url = state.router.location.pathname
        // 获取当前权限所支持url列表
        var permissions = state.AuthReduce.data.permissions || []
        // 遍历该列表是否包含当前url
        for (let [index, ele] of permissions.entries()) {
            if (url === ele.url) 
                return true
        }
        return false
    }
})

class Top_Sider_Nav extends React.Component {
    componentWillMount () {
       // 获取所有的handle
       const { loadMenu, loadAuth } = this.props
       // 加载菜单
       loadMenu()
       // 加载权限
       loadAuth()
    }    
    render () {
        // 获取所有的state
        const { menuList, authList } = this.props
        // 获取权限列表
        let permissions = authList.permissions || []
        // 当前url
        let currUrl = this.props.location.pathname
        // 如果是在首页，就显示空，否则显示一个加载图
        let myroute = currUrl === '/' ? <div></div> : <Spin />
        // 我目前的做法是，左侧列表依然存在，右侧界面需要等待权限加载完毕才可以渲染
        if (permissions.length) {
            myroute = (<div>
                <Route path = '/user/add'  component = { userIsAuthenticated(AddUser)        }/>
                <Route path = '/user/list' component = { userIsAuthenticated(ListUser)       }/>
                <Route path = '/error'     component = { MessageHoc('您没有权限')(BaseError) }/> 
            </div>)
        }
        // 默认激活项(必须是数组)
        let defaultSelectedKeys = ['0001']
        // 遍历该列表是否包含当前url
        for (let [index, ele] of menuList.entries()) {
            // 子列表
            for (let [index2, ele2] of ele.subMenu.entries()) {
                if (currUrl === ele2.link) {
                    defaultSelectedKeys = [ele2.id]    
                    // 设置一下localStorage路由（简陋版）
                    window.localStorage.setItem('prev_normal_url_id', ele2.id)                
                } else if (currUrl === '/error') {
                    // 如果是出现error页面，那么就使用上一个正常的页面的id
                    defaultSelectedKeys = [window.localStorage.getItem('prev_normal_url_id')]
                }
            }
        }

        // 开始渲染
        return  <Layout>
                    <Header className  = 'header'>
                        <div className = 'logo' />
                        <Menu
                            theme = 'dark'
                            mode  = 'horizontal'
                            defaultSelectedKeys = {['1']}
                            style = {{ lineHeight: '64px' }}
                        >
                            <Menu.Item key = '1'> nav 1 </Menu.Item>
                            <Menu.Item key = '2'> nav 2 </Menu.Item>
                            <Menu.Item key = '3'> nav 3 </Menu.Item>
                        </Menu>
                    </Header>
                    <Layout>
                        <Sider width = { 200 } style = {{ background: '#fff' }}>
                            <Menu
                                mode = 'inline'
                                defaultSelectedKeys = { defaultSelectedKeys }
                                selectedKeys = { defaultSelectedKeys }
                                defaultOpenKeys = {['1000', '1010']}
                                style = {{ height: '100%', borderRight: 0 }}
                            >
                                {
                                    menuList.map(item => {
                                        return <SubMenu key = { item.id } title = { <span><Icon type = { item.icon } />{ item.name }</span> }>
                                            {
                                                item.name === '用户管理' && <Menu.Item key = '0001' ><Link to = '/'>首页</Link></Menu.Item>
                                            }
                                            {                                                
                                                item.subMenu.map(sub => {
                                                    return <Menu.Item key = { sub.id }><Link to = { sub.link }>{ sub.name }</Link></Menu.Item>
                                                })
                                            }
                                        </SubMenu>
                                    })
                                }
                            </Menu>
                        </Sider>
                        <Layout style = {{ padding: '0 24px 24px' }}>
                            <Breadcrumb style = {{ margin: '12px 0' }}>
                                <Breadcrumb.Item> Home </Breadcrumb.Item>
                                <Breadcrumb.Item> List </Breadcrumb.Item>
                                <Breadcrumb.Item> App  </Breadcrumb.Item>
                            </Breadcrumb>
                            <Content style = {{ background: '#fff', padding: 24, margin: 0, minHeight: 280 }}>
                                <Route exact path = '/' component = {() => {
                                    return <div>
                                        <h2>测试组件首页</h2>
                                    </div>
                                }}/>
                                { myroute }
                            </Content>
                        </Layout>
                    </Layout>
                </Layout>
    }
}

function mapStateToProps (state) {
    return {
        // @Reduces/MenuReduce.js
        menuList: state.MenuReduce.data,

        // @Reduces/AuthReduce.js
        authList: state.AuthReduce.data
    }
}

function mapDispatchToProps (dispatch) {
    return {
        loadMenu: () => {
            // @Sagas/MenuSaga.js
            dispatch({ type: 'INIT_LOAD_MENU' })
        },
        loadAuth: () => {
            // @Sagas/AuthSaga.js
            dispatch({ type: 'GET_USER_AUTH' })
        }
    }
}

const App = connect(mapStateToProps, mapDispatchToProps)(Top_Sider_Nav)

export default withRouter(App)
```

---

> Reduces 系列

新建Reduces/MenuReduce.js

```js
export default (state = { data: [], selectedKeys: [], }, action) => {
    switch (action.type) {
        case 'INIT_LOAD_MENU_SUCCESS':
            return Object.assign({}, state, { data: action.data })
        default:
            return state
    }
}
```

新建Reduces/AuthReduce.js

```js
export default (state = { data: [] }, action) => {
    switch (action.type) {
        case 'SET_USER_AUTH':
            return Object.assign({}, state, { data: action.data })
        default:
            return state
    }
}
```

---

> Sagas 系列

新建Sagas/AuthSaga.js

```js
import { call, put, takeEvery, select, take, fork, cancel, cancelled } from 'redux-saga/effects'
import AuthApi from '@Api/AuthApi'

export default function* () {
    // 定义【 GET_USER_AUTH 】任务
    const action = yield take('GET_USER_AUTH')
    // ajax
    const data = yield call(AuthApi.getAuthData)
    // 取出成功 @Reduces/AuthReduce.js
    yield put({ type: 'SET_USER_AUTH', data })
}
```

新建Sagas/MenuSaga.js

```js
import { call, put, takeEvery, select, take, fork, cancel, cancelled } from 'redux-saga/effects'
import MenuApi from '@Api/MenuApi'

export default function* () {
    // 定义【 加载菜单 INIT_LOAD_MENU 】任务
    const action = yield take('INIT_LOAD_MENU')
    // ajax
    const data = yield call(MenuApi.getMenuData)
    // 取出成功，调用@Sagas/MenuSaga.js
    yield put({ type: 'INIT_LOAD_MENU_SUCCESS', data: data })
}
```

main.js

```js
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore, combineReducers, applyMiddleware } from 'redux'
import { ConnectedRouter, routerReducer, routerMiddleware } from 'react-router-redux'
import { Provider } from 'react-redux'
import createSaga from 'redux-saga'
import createHistory from 'history/createHashHistory'
import MenuReduce from '@Reduces/MenuReduce'
import AuthReduce from '@Reduces/AuthReduce'
import MenuSaga from '@Sagas/MenuSaga'
import AuthSaga from '@Sagas/AuthSaga'
import Layout_TSN from '@Components/layout/top_sider_nav'

const history = createHistory()
const router_middleware = routerMiddleware(history)

let saga  = createSaga()
let store = createStore(combineReducers({
    MenuReduce: MenuReduce,
    AuthReduce: AuthReduce,
    router: routerReducer
}), applyMiddleware(router_middleware, saga))
saga.run(MenuSaga)
saga.run(AuthSaga)

ReactDOM.render(
    <Provider store = { store }>
        <ConnectedRouter history = { history }>
            <Layout_TSN />
        </ConnectedRouter>
    </Provider>,
    document.getElementById('root')
)
```



![](/assets/adsasdsadgghjhkjhgfhhkjhldjd.png)

