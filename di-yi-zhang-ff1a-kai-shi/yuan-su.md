由于搭建了babel-preset-react，允许在代码中使用jsx语法。也就是在js中书写html标签

> JSX 的基本语法规则：
>
> 遇到 HTML 标签（以 &lt; 开头），就用 HTML 规则解析；
>
> 遇到代码块（以 { 开头），就用 JavaScript 规则解析。

注意，元素必须是闭合标签的，如果多个元素时，必须是被某个元素包裹。

> 错误示范：&lt;h1&gt;、&lt;input value='123'&gt; 、 &lt;a&gt;&lt;/a&gt;&lt;span&gt;&lt;/span&gt;
>
> 正确示范：&lt;h1&gt;&lt;/h1&gt;、&lt;input value='123' /&gt;、&lt;div&gt;&lt;a&gt;&lt;/a&gt;&lt;span&gt;&lt;/span&gt;&lt;/div&gt;

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

甚至支持直接通过大括号插入script脚本。

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

