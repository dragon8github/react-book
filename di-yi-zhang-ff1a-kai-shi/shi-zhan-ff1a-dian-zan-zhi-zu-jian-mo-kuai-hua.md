对上一节内容进行简单扩展，分离组件

```
$ cd /src && mkdir compooents && cd components
$ touch Agree.js News.js
```

main.js 修改为

```js
import React from 'react';
import ReactDOM from 'react-dom'
import News from './components/News'

ReactDOM.render(
    <News newsid = '101' />,
    document.getElementById('root')
);
```

./components/News.js 添加代码

```js
import React from 'react';
import axios from "axios"
import qs from 'qs'
import Agree from './Agree.js'

export default class News extends React.Component {
    constructor (props) {
        super(props)
        this.state = {
            agreeNum: 0
        }
    }
    agreeSubmit (obj) {
        axios.post("http://localhost:8080/news.php", qs.stringify({
            newsid: obj.props.newsid  
        })).then((res) => {
            this.setState({
                agreeNum: res.data.agree
            })
        })
    }
    componentWillMount () {
        axios.get("http://localhost:8080/news.php", {
            params: {
                newsid: this.props.newsid
            }
        }).then((res) => {
            this.setState({
                agreeNum: res.data.agree
            })
        })
    }
    render () {
        return <div>
            <h1>这是一篇新闻,新闻ID是101</h1>
            <Agree agreeNum = {this.state.agreeNum} />            
            <input type = "button" value = "我要点赞" onClick = {() => {
                this.agreeSubmit(this)
            }}/>
        </div>
    }
}
```

./components/Agree.js 添加代码

```js
import React from 'react';

export default class Agree extends React.Component {
    componentWillReceiveProps (newProp) {
        console.log("原有的：" + this.props.agreeNum + ", 新的：" + newProp.agreeNum)
    }
    render () {
        return <div>
            <h2>点赞数：{ this.props.agreeNum }</h2>
        </div>
    }
}
```

无须担心反复 import 重复的资源导致打包变大。webpack会智能处理。不可能重复引入。所以大可放心处理。

知识点：

当传入的props发生变化时，触发。往往我们可以在这个函数中来修改state让子组件重新渲染。

```js
componentWillReceiveProps (newProp) {
    console.log("原有的：" + this.props.agreeNum + ", 新的：" + newProp.agreeNum)
}
```

![](/assets/s88z8zx8xz8cz8xc.png)

