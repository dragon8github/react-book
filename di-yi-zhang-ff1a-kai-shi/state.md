state是实现双向数据绑定的桥梁

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
    componentWillUnmount() {
        clearInterval(this.timerID);
    }
    componentDidMount() {
        this.timerID = setInterval(() => this.tick(), 1000);
    }
    tick() {
        this.setState({
            date: new Date()
        });
    }
    render() {
        return ( <
            div >
            <
            h1 > Hello, world! < /h1> <
            h2 > It is { this.state.date.toLocaleTimeString() }. < /h2> <
            /div>
        );
    }
}

ReactDOM.render( <
    Clock / > ,
    document.getElementById('root')
);
```

![](/assets/1231251312512135213551123.png)

