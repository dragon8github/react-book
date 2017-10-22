Ant Design 传送门

> &lt;官网&gt;
>
> [https://ant.design/index-cn](https://ant.design/index-cn)
>
> &lt;Github&gt;
>
> [https://github.com/ant-design/ant-design/](https://github.com/ant-design/ant-design/)
>
> &lt;手册与指引&gt;
>
> [https://ant.design/docs/react/introduce-cn](https://ant.design/docs/react/introduce-cn)

根据官方指引，添加 babel-plugin-import 和 Ant Design 框架

```js
yarn add babel-plugin-import
yarn add antd
```

.babelrc 修改

```js
{
  "presets": ["react"],
  "plugins": [
    ["import", { "libraryName": "antd", "style": "css" }]
  ]
}
```

添加/components/layout/top\_sider\_nav.js

```js
import React from 'react'
import { connect } from 'react-redux'
import { HashRouter as Router, Route, Link } from 'react-router-dom'
import { Layout, Menu, Breadcrumb, Icon } from 'antd'
const { SubMenu } = Menu
const { Header, Content, Sider } = Layout

class Top_Sider_Nav extends  React.Component {
    render () {
        return <Router>
             <Layout>
            <Header className  = 'header'>
                <div className = 'logo' />
                <Menu
                    theme = 'dark'
                    mode  = 'horizontal'
                    defaultSelectedKeys = {['2']}
                    style = {{ lineHeight: '64px' }}>
                        <Menu.Item key = '1'>nav 1</Menu.Item>
                        <Menu.Item key = '2'>nav 2</Menu.Item>
                        <Menu.Item key = '3'>nav 3</Menu.Item>
                </Menu>
            </Header>
            <Layout>
                <Sider width = { 200 } style = {{ background: '#fff' }}>
                    <Menu
                        mode = 'inline'
                        defaultSelectedKeys = {['1']}
                        defaultOpenKeys = {['100','101']}
                        style = {{ height: '100%', borderRight: 0 }}>
                            <SubMenu key = 'sub1' title = {<span><Icon type = 'user' />subnav 1</span>}>
                                <Menu.Item key = '1'>option1</Menu.Item>
                                <Menu.Item key = '2'>option2</Menu.Item>
                                <Menu.Item key = '3'>option3</Menu.Item>
                                <Menu.Item key = '4'>option4</Menu.Item>
                            </SubMenu>
                            <SubMenu key = 'sub2' title = {<span><Icon type = 'laptop' />subnav 2</span>}>
                                <Menu.Item key = '5'>option5</Menu.Item>
                                <Menu.Item key = '6'>option6</Menu.Item>
                                <Menu.Item key = '7'>option7</Menu.Item>
                                <Menu.Item key = '8'>option8</Menu.Item>
                            </SubMenu>
                    </Menu>
                </Sider>
                <Layout style = {{ padding: '0 24px 24px' }}>
                    <Breadcrumb style = {{ margin: '12px 0' }}>
                        <Breadcrumb.Item>Home</Breadcrumb.Item>
                        <Breadcrumb.Item>List</Breadcrumb.Item>
                        <Breadcrumb.Item>App</Breadcrumb.Item>
                    </Breadcrumb>
                    <Content style={{ background: '#fff', padding: 24, margin: 0, minHeight: 280 }}>
                        <Route exact path = '/' component={() => {
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

export default Top_Sider_Nav
```

main.js

```js
import React from "react"
import ReactDOM from "react-dom"
import  Layout_TSN from "./components/layout/top_sider_nav" 

ReactDOM.render(
    <Layout_TSN />,
    document.getElementById('root')
);
```

![](/assets/aadsddddddasdasdasijijxzcjzicant.png)

