点击事件示例

```js
import React from 'react';
import ReactDOM from 'react-dom';

let obj = {
    name: "Lee",
    age: "24"
}

class Me extends React.Component
{
    showMe() {
        window.alert(123)
    }
    render()
    {
        return <div>
            <h2>{this.props.name}:{this.props.age}</h2>
            <input type="button" value="点我" onClick={this.showMe} />
        </div>
    }
}

ReactDOM.render(
    <Me { ...obj } />,
    document.getElementById('root')
);

```

![](/assets/123541245312551assad.png)

