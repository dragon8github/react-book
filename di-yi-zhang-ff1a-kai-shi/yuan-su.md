由于支持jsx，可以产生很多奇特的写法

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

