新建userauth.php，模拟权限分配

```php
<?php
header('Access-Control-Allow-Origin:*');  
header('Access-Control-Allow-Methods:GET,POST');  
header('Access-Control-Allow-Headers:x-requested-with,content-type'); 
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

新建Components/Hoc.js，专门生产高阶函数

```js
import React from 'react'

function MessageHoc (msg) {
    // 这里的 message 是约定好的
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

main.js

```js
import React from 'react'
import { Layout, Menu, Breadcrumb, Icon } from 'antd'
import { HashRouter as Router, Route, Link } from 'react-router-dom'
import { connectedRouterRedirect } from 'redux-auth-wrapper/history4/redirect'
import { withRouter } from 'react-router'
import { connect }    from 'react-redux'
import { BaseError }  from "@Components/Error"
import { Hoc }  from "@Components/Hoc"
import actions  from '@Actions/CommonAction'
import AddUser  from "@Components/user/addUser"
import ListUser from "@Components/user/listUser"

const { SubMenu } = Menu
const { Header, Content, Sider } = Layout

const userIsAuthenticated = connectedRouterRedirect({
    redirectPath: '/error',
    authenticatedSelector: state => {
        console.log(state)
        return false
    }
})

class Top_Sider_Nav extends React.Component {
    componentWillMount () {
       const { loadMenu } = this.props
       loadMenu()
    }
    render () {
        const { menuList } = this.props
        return <Layout>
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
                                <Route path = '/user/add'  component = { userIsAuthenticated(AddUser)  }/>
                                <Route path = '/user/list' component = { userIsAuthenticated(ListUser) }/>
                                <Route path = '/error'     component = { Hoc('您没有权限')(BaseError)  }/>
                            </Content>
                        </Layout>
                    </Layout>
                </Layout>
    }
}

function mapStateToProps (state) {
    return {
        // @Reduces/MenuReduce.js
        menuList: state.MenuReduce.data
    }
}

function mapDispatchToProps (dispatch) {
    return {
        loadMenu: () => {
            // @Sagas/MenuSaga.js
            // INIT_LOAD_MENU
            dispatch(actions.LoadMenu())
        }
    }
}

const App = connect(mapStateToProps, mapDispatchToProps)(Top_Sider_Nav)

export default withRouter(App)
```



