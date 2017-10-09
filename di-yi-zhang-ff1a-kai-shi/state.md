state是React中实现双向数据绑定的桥梁

> 注意，不要尝试手动修改this.state，必须使用this.setState来更新state
>
> 错误示范：this.state.comment = 'Hello';
>
> 正确示范：this.setState\({ comment: 'Hello' }\);

```js
import React from 'react';
import ReactDOM from 'react-dom';

class Clock extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            date: new Date()
        };
    }
    tick() {
        this.setState({
            date: new Date()
        });
    }
    componentWillUnmount() {
        clearInterval(this.timerID);
    }
    componentDidMount() {
        this.timerID = setInterval(() => { this.tick() }, 1000);
    }
    render() {
        return (<div>
                <h1>Hello, world!</h1> 
                <h2>It is { this.state.date.toLocaleTimeString() }.</h2> 
        </div>);
    }
}

ReactDOM.render( 
    <Clock /> ,
    document.getElementById('root')
);
```

![](/assets/1231251312512135213551123.png)

---

> 相关文档：
>
> [https://reactjs.org/docs/react-component.html](https://reactjs.org/docs/react-component.html)



