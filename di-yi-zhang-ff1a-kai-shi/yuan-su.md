由于支持jsx，可以产生很多奇特的写法。

> 注意，元素必须是闭合标签的，如果多个元素时，必须是被某个元素包裹。
>
> 正确示例： &lt;div&gt;&lt;/div&gt;、&lt;h1&gt;&lt;/h1&gt;、&lt;input /&gt;、&lt;div&gt;&lt;a&gt;&lt;/a&gt;&lt;span&gt;&lt;/span&gt;&lt;/div&gt;
>
> 错误示例：&lt;input value='123'&gt; 、 &lt;a&gt;&lt;/a&gt;&lt;span&gt;&lt;/span&gt;

```js
import React from 'react';
import ReactDOM from 'react-dom';

let me = <h1>Lee</h1>
ReactDOM.render(
    me,
    document.getElementById('root')
);
```

![](/assets/123asdasdas.png)

---

甚至支持直接通过大括号插入script脚本，但只支持表达式。

```js
import React from 'react';
import ReactDOM from 'react-dom';

let me = <h1>Lee{alert(1)}</h1>
ReactDOM.render(
      me,
      document.getElementById('root')
);
```

![](/assets/zxzcxz.png)

