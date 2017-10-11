前两节课的路由内容中，我们发现一旦刷新页面，就会出现类似404的页面，表示找不到此页面。

![](/assets/wuashusdauhsaduhadshu.png)

这是为什么呢？

这其实和路由的原理，浏览器的 HTML5 History API 有关。先来试试下面的内容：

> 注意，如果直接打开静态页面[ file:///C:/Users/lizhaohong/Desktop/test\_history.html](file:///C:/Users/lizhaohong/Desktop/test_history.html) 是没有效果的。
>
> 必须在服务器部署的环境下打开才可以使用，如 [http://localhost:8080/test\_history.html](http://localhost:8080/test_history.html)

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>    
</head>
<body>
<button onclick="updateUrl('abc')" >abc</button>
<button onclick="updateUrl('efg')" >efg</button>
<button onclick="history.back()">回退</button>
<button onclick="history.forward()">前进</button>
<script>
    // 使用 HTML5 History API  修改浏览器地址
    var updateUrl = function (path) {
        let state = {
            url: path,
            name: "fuckyou"
        };
        history.pushState(state, null, path);
    }

    // 监听页面的真实跳转
    window.onpopstate = function(event) {
        alert("location: " + document.location + ", state: " + JSON.stringify(event.state));
    };
</script>
</body>
</html>
```

通过上demo我们发现，**HTML5 History API  可以修改浏览器URL地址，而并没有真实请求访问该地址的资源**。

而当我们使用回退或前进时，会按照正常的页面跳转逻辑更新url地址，并且还会触发 window.onpopstate 。

通过这两点我们大概可以猜出 React Router 的实现原理。

而当我们处于 HTML5 History API  修改的地址如 [http://localhost:8080/abc](http://localhost:8080/abc) ，此时如果刷新页面，浏览器会去请求该地址的资源。如果服务器资源不存在。当然会报错咯。

---

但 HTML5 History API 必须是支持 html5 的浏览器才可以使用，所以兼容性差强人意。所以有另一种更可靠的代替方案：

Hash 路由模式。此模式历史发展久远，而且相对成熟，不需要过多介绍，直接看例子即可：

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<button href="#user" style="cursor: pointer">进入user</button>
<button href="#news" style="cursor: pointer">进入news</button>
<script>
    window.onhashchange = function () {
        alert(window.location.hash)
    }
</script>
</body>
</html>
```

![](/assets/21323ashtyuuioihjhj.png)

事实上，React Router 也支持 Hash 路由模式。只需要修改 BrowserRouter 为 HashRouter 即可：

```js
// 修改前
import { BrowserRouter as Router, Route, Link } from 'react-router-dom'

// 修改后
import { HashRouter as Router, Route, Link } from 'react-router-dom'
```

![](/assets/hashroutersdadasdas.png)

我们惊奇的发现，在Hash模式下，刷新页面是可以继续预览的。而不会出现404界面。

当然这和锚点\#是有关系的。所以我们通常建议使用Hash模式投入到生产环境中。

