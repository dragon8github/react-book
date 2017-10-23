webpack、babel、react 等环境搭建的配置请参考：[《脚手架搭建》](https://dragon8github.gitbooks.io/react/content/chapter1.html)

新建Menu.php

```php
<?php
header('Access-Control-Allow-Origin:*');  
header('Access-Control-Allow-Methods:GET,POST');  
header('Access-Control-Allow-Headers:x-requested-with,content-type'); 

class Menu {
    // 菜单ID，这些都是从数据库取
    public $id = 0;
    // 菜单名
    public $name = '';
    // 连接地址
    public $link = '';
    // 图标
    public $icon = '';
    // 子菜单，是一个列表,必须初始化为一个空数组，否则前端会出错
    public $subMenu = []; 
    // 构造函数
    public function __construct($id, $n, $l, $icon = '') {
        $this->id   = $id;
        $this->name = $n;
        $this->link = $l;
        $this->icon = $icon;
    }
}

// 菜单项目
$userMang = new Menu('1000', '用户管理', '', 'user');
$userAdd  = new Menu('1001', '用户新增', '/user/add');
$userList = new Menu('1002', '用户列表', '/user/list');
$userMang->subMenu = [$userAdd, $userList];

// 站内信管理
$msgMang = new Menu('1010', '站内信管理', '', 'laptop');
$msgAdd  = new Menu('1011', '发布站内信', '/msg/add');
$msgList = new Menu('1012', '站内信列表', '/msg/list');
$msgMang->subMenu = [$msgAdd, $msgList];

header('Content-type:application/json');
exit(json_encode([$userMang,$msgMang]));
```

新建Api/ApiConfig.js

```js
export const apiHost = 'http://localhost:8080'

export default {
    menu: '/Menu.php'
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

新建Actions/CommonAction.js

```js
export default class CommonAction {
    // 加载菜单
    static LoadMenu () {
        return {
            type: 'INIT_LOAD_MENU'
        }
    }
    // 加载成功
    static LoadMenuSUCCESS (data) {
        return {
            type: 'INIT_LOAD_MENU_SUCCESS',
            payload: data
        }
    }
}
```

新建Reduces/MenuReduce.js

```js
import actions from '@Actions/CommonAction'

export default (state = { data: [] }, action) => {
    switch (action.type) {
        // INIT_LOAD_MENU_SUCCESS
        case actions.LoadMenuSUCCESS().type:
            return Object.assign({}, state, { data: action.payload })
        default:
            return state
    }
}
```

新建Sagas/MenuSaga.js

```js
import { call, put, takeEvery, select, take, fork, cancel, cancelled } from 'redux-saga/effects'
import actions from '@Actions/CommonAction'
import MenuApi from '@Api/MenuApi'

export default function* () {
    // 定义【加载菜单】任务
    const action = yield take(actions.LoadMenu().type)
    // ajax
    const result = yield call(MenuApi.getMenuData)
    // 取出成功
    yield put(actions.LoadMenuSUCCESS(result))
}
```

新建Components/layout/top\_sider\_nav.js

```js
import { Layout, Menu, Breadcrumb, Icon } from 'antd'
import React from 'react'
import { connect } from 'react-redux'
import { HashRouter as Router, Route, Link } from 'react-router-dom'
import actions from '@Actions/CommonAction'
const { SubMenu } = Menu
const { Header, Content, Sider } = Layout

class Top_Sider_Nav extends React.Component {
    componentWillMount () {
       const { loadMenu } = this.props
       loadMenu()
    }
    render () {
        const { menuList } = this.props
        return <Router>
                    <Layout>
                        <Header className  = 'header'>
                            <div className = 'logo' />
                            <Menu
                                theme = 'dark'
                                mode  = 'horizontal'
                                defaultSelectedKeys = {['2']}
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
                                    defaultSelectedKeys = {['1']}
                                    defaultOpenKeys = {['100', '101']}
                                    style = {{ height: '100%', borderRight: 0 }}
                                >
                                    {
                                        menuList.map(item => {
                                            return <SubMenu key = { item.id } title = { <span><Icon type = { item.icon } />{ item.name }</span> }>
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
                                <Content style={{ background: '#fff', padding: 24, margin: 0, minHeight: 280 }}>
                                    <Route exact path = '/' component = {() => {
                                        return <div>
                                            <h2>测试组件首页</h2>
                                        </div>
                                    }}/>
                                </Content>
                            </Layout>
                        </Layout>
                    </Layout>
        </Router>
    }
}

function mapStateToProps (state) {
    return {
        menuList: state.MenuReduce.data
    }
}

function mapDispatchToProps (dispatch) {
    return {
        loadMenu: () => {
            dispatch(actions.LoadMenu())
        }
    }
}

const App = connect(mapStateToProps, mapDispatchToProps)(Top_Sider_Nav)

export default App
```

main.js

```js
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore, combineReducers, applyMiddleware } from 'redux'
import { Provider} from 'react-redux'
import createSaga from 'redux-saga'
import { ConnectedRouter, routerReducer, routerMiddleware } from 'react-router-redux'
import createHistory from 'history/createHashHistory'
import MenuReduce from '@Reduces/MenuReduce'
import MenuSaga from '@Sagas/MenuSaga'
import Layout_TSN from '@Components/layout/top_sider_nav'

const history = createHistory()
const router_middleware = routerMiddleware(history)

let saga  = createSaga()
let store = createStore(combineReducers({
    MenuReduce: MenuReduce,
    router: routerReducer
}), applyMiddleware(router_middleware, saga))
saga.run(MenuSaga)

ReactDOM.render(
    <Provider store = { store }>
        <ConnectedRouter history = { history }>
            <Layout_TSN />
        </ConnectedRouter>
    </Provider>,
    document.getElementById('root')
)
```

![](/assets/impasdasdasdasqweweqehghjghghghort.png)

