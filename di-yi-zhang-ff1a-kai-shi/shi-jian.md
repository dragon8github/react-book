点击事件示例

> 注意，onClick中的方法名不需要加\(\)，否则就自动执行该方法了

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

---

也可以在标签中直接书写function、Es2015的箭头函数

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
            <input type="button" value="点我" onClick={() => {
                alert(this.props.name)
            }} />
        </div>
    }
}

ReactDOM.render(
    <Me { ...obj } />,
    document.getElementById('root')
);
```

![](/assets/阿桑23aasdasd.png)

> 注意，由于箭头函数没有this，会往父辈寻找object对象作为this。
>
> 本demo将class Me 作为了 this，所以才能正常找到this.props.name。
>
> 如果是正常的function，this指向的是函数本身。是找不到this.props.name的。解决方法是使用function.prototype.bind\(obj\)

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
            <input type="button" value="点我" onClick={function () {
                alert(this.props.name)
            }.bind(this)} />
        </div>
    }
}

ReactDOM.render(
    <Me { ...obj } />,
    document.getElementById('root')
);
```



