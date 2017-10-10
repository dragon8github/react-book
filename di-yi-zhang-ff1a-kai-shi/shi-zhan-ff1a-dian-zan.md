本节课实现新闻点赞功能。

老规矩先使用php搭建后台接口。我们使用redis代替数据库

window下使用redis，以及phpstudy如何添加redis的扩展，请参考以下教程

> 《phpstudy + redis 搭建教程》： [http://blog.csdn.net/tianjingang1/article/details/68491369](http://blog.csdn.net/tianjingang1/article/details/68491369)
>
> 《php\_redis.dll 资源下载》：[http://windows.php.net/downloads/pecl/releases/redis/2.2.7/](http://windows.php.net/downloads/pecl/releases/redis/2.2.7/)
>
> 《php\_igbinary.dll 资源下载》： [http://pecl.php.net/package/igbinary](http://pecl.php.net/package/igbinary)
>
> 《window版redis 下载地址》：[https://github.com/ServiceStack/redis-windows/raw/master/downloads/redis-latest.zip](https://github.com/ServiceStack/redis-windows/raw/master/downloads/redis-latest.zip)
>
> 《redis使用教程》：[http://www.cnblogs.com/CyLee/p/7193278.html](http://www.cnblogs.com/CyLee/p/7193278.html)

news.php的代码：

```php
<?php
header("Access-Control-Allow-Origin:*"); 
header('Access-Control-Allow-Headers:x-requested-with,content-type'); 

$redis = new Redis();
$redis->connect("127.0.0.1",6379);

function agree ($newsid) {
    global $redis;
    $get_num = $redis->hget("newsagree", "news".$newsid);

    if ($get_num)
        $get_num++;
    else
        $get_num = 1;

    $redis->hset("newsagree", "news".$newsid, $get_num);
    return $get_num;
}

function getAgree($newsid) {
    global $redis;
    $get_num = $redis->hget("newsagree", "news".$newsid);

    if($get_num)
        return $get_num;
    else
        return 0;
}


// 代表点赞, 要为新闻的点赞数++
if (isset($_POST["newsid"])) {   
    $agreeNum = agree($_POST["newsid"]);
    $arr = array("status" => "success", "agree" => $agreeNum);
    $strjson = json_encode($arr);   
    exit($strjson);
}

// 代表获取新闻点赞数
if (isset($_GET["newsid"])) { 
    $agreeNum = getAgree($_GET["newsid"]);
    $arr = array("agree" => $agreeNum);
    $strjson = json_encode($arr);   
    exit($strjson);
}

exit("");
```

main.js的代码：

```js
import React from 'react';
import ReactDOM from 'react-dom'
import axios from "axios"
import qs from 'qs'

class News extends React.Component {
    constructor (props) {
        super(props)
        this.state = {
            agreeNum: 0
        }
    }
    agreeSubmit (obj) {
        axios.post("http://localhost:8080/news.php", qs.stringify({
            newsid: obj.props.newsid  
        })).then((res) => {
            this.setState({
                agreeNum: res.data.agree
            })
        })
    }
    componentWillMount () {
        axios.get("http://localhost:8080/news.php", {
            params: {
                newsid: this.props.newsid
            }
        }).then((res) => {
            this.setState({
                agreeNum: res.data.agree
            })
        })
    }
    render () {
        return <div>
            <h1>这是一篇新闻,新闻ID是101</h1>
            <h2>{ this.state.agreeNum }</h2>
            <input type = "button" value = "我要点赞" onClick = {() => {
                this.agreeSubmit(this)
            }}/>
        </div>
    }
}

ReactDOM.render(
    <News newsid = '101' />,
    document.getElementById('root')
);
```

利用 qs.stringify\({ newsid: obj.props.newsid }\), 将对象转化为 a=999&b=333&c=555 这样的数据格式。

这样一来请求方式自动转换以 application/x-www-form-urlencoded 方式提交。这也是推荐的一种提交方式。

这样做的好处是，php默认是识别 application/x-www-form-urlencoded 方式提交的数据，可以通过$\_POST直接获取操作。

如果是使用 application/json 方式提交的数据，$\_POST是获取不到的，需要先使用必须使用$GLOBALS\['HTTP\_RAW\_POST\_DATA'\]取出来，然后再json\_decode才可以。

具体可以参考这篇文章：[http://www.cnblogs.com/CyLee/p/7644380.html](http://www.cnblogs.com/CyLee/p/7644380.html)

值得一提的是，axios 默认的post提交方式就是 application/json ，为了避免这个麻烦，建议手动配置header为application/x-www-form-urlencoded 方式提交，具体参考官方手册：

> axios.defaults.headers.post\['Content-Type'\] = 'application/x-www-form-urlencoded';



