高阶组件的作用，就是能够尽可能的复用组件。

![](/assets/ 71a3efa3-9fd1-4aa1-960f-a32632415aa2import.png)

之前我们使用 import { connect } from 'react-redux'

const App = connect\(mapStateToProps, mapDispatchToProps\)\(Top\_Sider\_Nav\)

生成的其实就是一个高阶组件。

main.js

```js
import React from 'react'
import ReactDOM from 'react-dom'

class Test extends React.Component {
    constructor (props) {
        super(props)
    }
    render () {
        const { title, datalist, showTest } = this.props

        return <div>
            <h2> { title } abc </h2>
            <ul>
                {
                    datalist.map(item => {
                        return <li key = { item }> { item } </li>
                    })
                }
            </ul>
            <input type = 'button' value = '测试方法' onClick = { showTest }/>
        </div>
    }
}

function Hoc (mapStateToProps, mapDispatchToHandler) {

    const props = Object.assign({}, mapStateToProps(), mapDispatchToHandler())

    return function (Component) {
        return class extends React.Component {
            render () {
                return <Component {...props} />
            }
        }
    }
}

function mapStateToProps () {
    return {
        title: '用户列表',
        datalist: ['张三', '李四']
    }
}

function mapDispatchToHandler () { 
    return {
        showTest: () => {
            alert('高阶组件测试方法')
        }
    }
}

const App = Hoc(mapStateToProps, mapDispatchToHandler)(Test)

ReactDOM.render(
    <App />,
    document.getElementById('root')
)
```

![](/assets/傲霜斗雪在程序标你妹你换个方法.png)

