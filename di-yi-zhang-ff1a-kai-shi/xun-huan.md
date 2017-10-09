使用map对数组进行遍历

> 对于React中的循环渲染来说，**key**属性是必备的，否则会报警告。虽然也不会造成什么大的影响。

```js
import React from 'react';
import ReactDOM from 'react-dom';

let obj = [
    {name: "Lee", age: "24"},
    {name: "Mp", age: "23"},
]

class Me extends React.Component
{
    render()
    {
        return <div>    
            {
                this.props.users.map((item, index) => {
                    return <h2 key = {index}>{item.name}:{item.age}</h2>
                })
            }
        </div>
    }
}

ReactDOM.render(
    <Me users = { obj } />,
    document.getElementById('root')
);
```

![](/assets/12yogid.png)

