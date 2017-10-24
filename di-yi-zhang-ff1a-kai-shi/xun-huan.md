使用map对数组进行遍历

> 对于React中的循环渲染来说，**key**属性是必备的，否则会报警告。虽然也不会造成什么大的影响。
>
> 重点：我们发现，react会自动对数组进行遍历渲染。

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

---

使用for循环

```js
class Test extends React.Component {
    render () {
        const { getCount, addInput } = this.props
        let listInput = () => {
            let result = []
            for (let i = 1; i <= 10; i++) {
               result.push(<div key = { i }>输入框: <input type='text'/></div>)
            }
            return result
        }

        return <div>
            { listInput() }
            <input type = 'button' onClick = { addInput }  value = '添加'/>
        </div>
    }
}
```

我们发现，只需要返回一个数组。就可以让react自动渲染出来。

