jsx中是无法直接使用If-else语句的。通常先定义函数或者变量来使用。参考以下文章的方案一二：

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

return JSX标签时注意两点：

1、只有函数才需要 return，如果是表达式是不需要return的，直接书写出标签就可以渲染了。这个要区分清楚，比如三元表达式或者 && 运算符

2、如果 return 多行标签，记得套上一个 div 和 \(\)，毕竟div套上多少个也不会有所影响。这个也算JSX基础知识了。

```js
{
    return (
        <div>
            <Menu.Item key = '1'> nav 1 </Menu.Item>
            <Menu.Item key = '2'> nav 2 </Menu.Item>
            <Menu.Item key = '3'> nav 3 </Menu.Item>
        </div>
    )
}
```

参考资料：[http://blog.csdn.net/pcaxb/article/details/53927761](http://blog.csdn.net/pcaxb/article/details/53927761)

