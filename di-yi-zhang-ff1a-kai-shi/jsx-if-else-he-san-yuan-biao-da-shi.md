> JSX 的基本语法规则：
>
> 遇到 HTML 标签（以 &lt; 开头），就用 HTML 规则解析；
>
> 遇到代码块（以 { 开头），就用 JavaScript 规则解析。

jsx中是无法直接使用If-else语句的。通常会定义类函数或者变量来使用。参考以下链接的方案一、方案二：

```
http://blog.csdn.net/wmzy1067111110/article/details/51538241
```

但事实上，还是有奇思淫巧可以使用的，譬如我们观察到map中就可以书写，那就可以这样：

```js
{
    [0].map(data => {
        if (condition) { 
            return <div></div>
        }
    })
}
```

或者我们可以使用闭包、IIFE立即执行函数的思路来书写if-else:

```js
// 方式一
{
  (
     () => {
        if (condition) { 
           console.log(this)
           return <div></div>
           return ...
        }
     }
  )()
}

// 方式二
{
  (
     function (self) {
        if (condition) { 
           console.log(self)
           return ...
        }
     }(this)
  )
}
```

其实简单的逻辑，完全可以靠三元表达式或者 && 运算符来代替

```js
// 方式一：三元表达式
{
   item.name === '用户管理' ? <div>新增用户</div> : null   
}

// 方式二：&&运算符
{
   item.name === '用户管理' && <div>新增用户</div> 
}
```



