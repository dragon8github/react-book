使用map对数组进行遍历

```js
import React from 'react';
import ReactDOM from 'react-dom';

let obj = [
    {name: "Lee", age: "24"},
    {name: "Mp", age: "23"},
]

class Me extends React.Component
{
    showMe() {
        window.alert(123)
    }
    render()
    {
        return <div>    
            {
                this.props.users.map((item) => {
                    return <h2>{item.name}:{item.age}</h2>
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

