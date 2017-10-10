React 中的组件其实就是 ES2015 中的类（Class）的语法，只需要继承 React.Component 即可。

> 注意，组件在定义和使用时，首字母必须大写

```js
import React from 'react';
import ReactDOM from 'react-dom';

class Me extends React.Component {
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

传递一个object类型的参数

```js
import React from 'react';
import ReactDOM from 'react-dom';

let obj = {
    name: "Lee",
    age: "24"
}

class Me extends React.Component {
    render() {
        return <h2>{this.props.fuck.name}:{this.props.fuck.age}</h2>
    }
}

ReactDOM.render(
    <Me fuck = { obj } />,
    document.getElementById('root')
);
```

---

使用ES2015的新语法对象结构进行参数传递，这样做的好处是，对比上个demo，可以省略属性名的书写

```js
import React from 'react';
import ReactDOM from 'react-dom';

let obj = {
    name: "Lee",
    age: "24"
}

class Me extends React.Component {
    render() {
        return <h2>{this.props.name}:{this.props.age}</h2>
    }
}

ReactDOM.render(
    <Me { ...obj } />,
    document.getElementById('root')
);
```



