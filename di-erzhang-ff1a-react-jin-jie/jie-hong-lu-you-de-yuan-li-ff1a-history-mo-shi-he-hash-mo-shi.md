前两节课的路由内容中，我们发现一旦刷新页面，就会出现类似404的页面，表示找不到此页面。

![](/assets/wuashusdauhsaduhadshu.png)

这是为什么呢？

这其实和路由的原理，浏览器的API window.history有关。先来试试下面的内容：

注意，以下页面只有在服务器环境下部署打开才可以使用，如 http://localhost:8080/test\_history.html

如果直接打开是没有效果的。

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
    // 使用 history API 修改浏览器地址
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



