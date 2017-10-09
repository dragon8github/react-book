相关传送门：

> &lt;axios github 地址&gt;： [https://github.com/axios/axios](https://www.gitbook.com/book/dragon8github/react/edit#)
>
> &lt;refs 官方文档&gt;：[https://reactjs.org/docs/refs-and-the-dom.html](https://reactjs.org/docs/refs-and-the-dom.html)
>
> ref的作用其实就是获取、操作dom元素。
>
> 在Vue中，是为DOM、标签、组件绑定一个名字，然后再通过this.$refs.name获取该DOM，再进一步操作。
>
> 在React中，是采用回调函数的方式获取并操作DOM。

先使用 phpstudy 快速创建 team.php 作为后端测试数据

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
            <span ref = {(ele) => {this.loadingbox.push(ele)}}> 正在加载... </span>
            {
                this.state.teammates.map((item, index) => {
                    return <h2 key = { index }> { item.name } —— { item.age } </h2>
                })
            }
            <h1>项目经理</h1>
            <span ref = {(ele) => {this.loadingbox.push(ele) }}> 正在加载... </span>
            { this.state.leader }
        </div>
    }
}

ReactDOM.render(
    <Team />,
    document.getElementById('root')
);
```



