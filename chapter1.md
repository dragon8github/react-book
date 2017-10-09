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
# 防止某些fq源的关系，设置淘宝镜像代理
yarn config set registry https://registry.npm.taobao.org

# 初始化yarn
yarn init -y

# 添加核心库
yarn add react react-dom webpack

# 添加webpack插件
yarn add html-webpack-plugin

# 添加babel插件
yarn add babel-preset-react
yarn add babel-loader babel-core
```

添加并且配置webpack.config.js

```js
var HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    entry: {
        main: __dirname + '/src/main'
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
            chunks:["main"]
        })
    ],
    module: {
      rules: [
        { test: /\.js$/, exclude: /node_modules/, loader: "babel-loader" }
      ]
    }
}
```

添加并且配置.babelrc

```js
{
    "presets": ["react"]
}
```



