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

main.js 代码

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

上面的代码会发现此错误

![](/assets/oriimdmamamsd.png)

调试流程：

1、判断是hideLoading出错

2、ele.style.display = 'none' 报错。原因是对NULL进行了操作。

3、loadingbox 数组中为何有6个成员，并且存在NULL。按照我们的理解应该只有两个成员不是吗？

4、发现是this.setState导致的，如果把this.hideLoading\(\) 放在它之前就不会导致错误

5、原因是this.setState导致的重新渲染 render\(\) 重新渲染时，分为两次，第一次 ref 的 ele 为NULL。第二次才正常的值。换句话说，整个过程一共渲染了三次，每次添加两个ele。并且第二次为两个为两个NULL，所以数组 lonadingbox一共具有6个成员。

