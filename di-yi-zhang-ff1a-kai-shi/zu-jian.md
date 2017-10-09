React 中的组件创建其实就是 ES2015 中的类语法，只需要继承 React.Component 即可。

> 注意，组件的首字母必须大写

```js
import React from 'react';
import ReactDOM from 'react-dom';

class Me extends React.Component
{
    render()
    {
        return <h2>{this.props.name}:{this.props.age}</h2>
    }
}

ReactDOM.render(
    <Me name="Lee" age="24"/>,
    document.getElementById('root')
);
```

![](/assets/asdajl.png)

---



