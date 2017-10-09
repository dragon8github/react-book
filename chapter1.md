各大传送门

```py
# react 官网
https://reactjs.org/

# codepen 快速测试react代码
https://codepen.io/gaearon/pen/ZpvBNJ?editors=0010

# yarn 包管理工具官网
https://yarnpkg.com/zh-Hans/

# babel 相关
http://babeljs.io/docs/setup/#installation
```

使用yarn安装必备库

```py
# 为了避免某些国外镜像源安装失败，先设置淘宝镜像代理
yarn config set registry https://registry.npm.taobao.org

# 初始化yarn
yarn init -y

# 添加核心库
yarn add react react-dom webpack

# 添加webpack插件
yarn add html-webpack-plugin
yarn add webpack-dev-server

# 添加babel插件
yarn add babel-preset-react
yarn add babel-loader babel-core
```

创建目录结构

```ruby
$ mkdir src && touch webpack.config.js .babelrc index.html
$ cd src && touch main.js && mkdir assets
```

配置webpack.config.js

```js
var HtmlWebpackPlugin = require('html-webpack-plugin')
var path = require('path')

module.exports = {
    entry: {
        main: __dirname + '/src/main.js'
    },
    output: {
        filename: '[name].js',
        path: __dirname + '/build'
    },
    plugins: [
        new HtmlWebpackPlugin({
            title: "my title",
            filename: __dirname + "/build/index.html",
            template: __dirname + "/index.html",
            chunks: ["main"]
        })
    ],
    module: {
        rules: [
            { test: /\.js$/, exclude: /node_modules/, loader: "babel-loader" }
        ]
    },
    devServer: {
        contentBase: path.join(__dirname, "dist"),
        compress: true,
        port: 9000
    }
}
```

配置.babelrc

```js
{
    "presets": ["react"]
}
```

配置package.json

```js
{
  //...
  "scripts": {
    "build": "webpack",
    "dev": "webpack-dev-server"
  }
}
```

编写index.html代码

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div id="root"></div>
</body>
</html>
```

编写main.js代码

```js
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);
```

启动项目，浏览器打开localhost:9000

```
npm run dev
```

![](/assets/123123.png)

