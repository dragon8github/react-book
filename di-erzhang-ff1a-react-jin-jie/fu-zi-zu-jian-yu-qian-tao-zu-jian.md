使用`this.props.children`将闭合在组件肚子中的内容取出来。

```js
import React from 'react';
import ReactDOM from 'react-dom';

class Me extends React.Component {
    render() {
        return <div>
            {this.props.children}
        </div>
    }
}

ReactDOM.render(
    <Me>
       哈哈哈哈哈哈哈啊 
    </Me>,
    document.getElementById('root')
);
```

![](/assets/488jvnvnnxcnjxjcn.png)

