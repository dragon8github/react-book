相关传送门：

> &lt;axios github 地址&gt;： [https://github.com/axios/axios](https://www.gitbook.com/book/dragon8github/react/edit#)
>
> &lt;refs 官方文档&gt;：[https://reactjs.org/docs/refs-and-the-dom.html](https://reactjs.org/docs/refs-and-the-dom.html)

使用 phpstudy 创建后端测试数据 team.php

```php
<?php 
    header("Access-Control-Allow-Origin:*");
    sleep(1);
    echo '{"leader":"shenyi","teammates":[{"name":"lisi","age":19},{"name":"zhangsan","age":20}]}';
?>
```

main.js代码

```js
import React from 'react'
import ReactDOM from 'react-dom'
import axios from "axios"

class Team extends React.Component {
    constructor (props) {
        super(props)
        this.loadingbox = []
        this.state = {
            leader    : "",
            teammates : []            
        }
    }
    showLoading () {
         this.loadingbox.forEach((ele) => ele.style.display = 'block')
    }
    hideLoading () {
        this.loadingbox.forEach((ele) => ele.style.display = 'none')
    }
    componentWillMount () {
        this.showLoading()
        axios.get("http://localhost:8080/team.php").then((res) => {
            this.setState({
                leader    : res.data.leader,
                teammates : res.data.teammates
            })
            this.hideLoading()
        })
    }
    render () {
        return <div>
            <h1>团队队员</h1>
            <span ref = {(ele) => {this.loadingbox.push(ele)}}>正在加载...</span>
            {
                this.state.teammates.map((item) => {
                    return <h2> { item.name } _ { item.age } </h2>
                })
            }
            <h1>项目经理 : </h1>
            <span ref = {(ele) => {this.loadingbox.push(ele)}}> 正在加载... </span>
            { this.state.leader }
        </div>
    }
}

ReactDOM.render(
    <Team />,
    document.getElementById('root')
);

```



