通过使用 withRouter 解决跳转路由但不渲染界面的问题。

原因是没有触动props变化，所以界面不会改变。withRouter 的使用可以解决这个问题。

> 但依然存在一个问题，为什么我在最外围套上一个 &lt;Route&gt; 的话，一样可以解决这个问题呢？
>
> 暂时想不透，先忽略这个问题吧。

添加组件1： Components/addUser.js

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

添加组件2： Components/listUser.js

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

修改Components/layout/top\_sider\_nav.js，使用 withRouter 来解决上述问题。

```js
import React from 'react'
import { Layout, Menu, Breadcrumb, Icon } from 'antd'
import { HashRouter as Router, Route, Link } from 'react-router-dom'
import { connectedRouterRedirect } from 'redux-auth-wrapper/history4/redirect'
import { withRouter } from 'react-router'
import { connect }    from 'react-redux'
import actions  from '@Actions/CommonAction'
import AddUser  from "@Components/user/addUser"
import ListUser from "@Components/user/listUser"

const { SubMenu } = Menu
const { Header, Content, Sider } = Layout

class Top_Sider_Nav extends React.Component {
    componentWillMount () {
       const { loadMenu } = this.props
       loadMenu()
    }
    render () {
        const { menuList } = this.props
        return  <Layout>
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
                            <Content style = {{ background: '#fff', padding: 24, margin: 0, minHeight: 280 }}>
                                <Route exact path = '/' component = {() => {
                                    return <div>
                                        <h2>测试组件首页</h2>
                                    </div>
                                }}/>
                                <Route path = '/user/add'  component = { AddUser  }/>
                                <Route path = '/user/list' component = { ListUser }/>
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

![](/assets/爱的飒飒的撒打算写做出选择下载现在1312312312312.png)

