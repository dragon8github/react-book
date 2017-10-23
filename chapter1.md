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

# 添加webpack
yarn add webpack

# 添加webpack插件
yarn add html-webpack-plugin
yarn add webpack-dev-server
yarn add extract-text-webpack-plugin

# 添加webpack-loader
yarn add style-loader css-loader node-sass sass-loader 
yarn add url-loader
yarn add file-loader
yarn add postcss-loader autoprefixer

# 添加babel插件
yarn add babel-preset-react
yarn add babel-loader babel-core
yarn add babel-plugin-import

# react相关
yarn add react react-dom
yarn add react-router react-router-dom
yarn add redux redux-thunk redux-saga react-redux 
yarn add react-router-redux@next history 
yarn add bundle-loader

# 蚂蚁金服的UI框架：Ant Design
yarn add antd

# 添加第三方库
yarn add axios
```

配置webpack.config.js

> 不要认为 Webpack 只适合单页面的构建
>
> 只需要配置多个 entry 以及 多个 HtmlWebpackPlugin 即可构建多个页面。

```js
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const ExtractTextPlugin = require('extract-text-webpack-plugin')
const autoprefixer = require('autoprefixer')

module.exports = {    
    entry: {
        main: __dirname + '/src/main.js'
    },
    output: {
        path: __dirname + '/dist',
        filename: 'assets/js/[name].js'
    },
    module: {
        rules: [
            { 
                test: /\.js$/, 
                exclude: /node_modules/, 
                loader: 'babel-loader' 
            },
            {
                test: /\.woff(\?v=\d+\.\d+\.\d+)?$/,
                loader: 'url-loader',
                options: {
                    limit: 10000,
                    minetype: 'application/font-woff',
                },
            },
            {
                test: /\.woff2(\?v=\d+\.\d+\.\d+)?$/,
                loader: 'url-loader',
                options: {
                    limit: 10000,
                    minetype: 'application/font-woff',
                },
            },
            {
                test: /\.ttf(\?v=\d+\.\d+\.\d+)?$/,
                loader: 'url-loader',
                options: {
                    limit: 10000,
                    minetype: 'application/octet-stream',
                },
            },
            { 
                test: /\.eot(\?v=\d+\.\d+\.\d+)?$/,
                loader: 'url-loader',
                options: {
                    limit: 10000,
                    minetype: 'application/vnd.ms-fontobject',
                },
            },
            {
                test: /\.svg(\?v=\d+\.\d+\.\d+)?$/,
                loader: 'url-loader',
                options: {
                    limit: 10000,
                    minetype: 'image/svg+xml',
                },
            },
            {
                test: /\.(png|jpg|jpeg|gif)(\?v=\d+\.\d+\.\d+)?$/i,
                loader: 'url-loader',
                options: {
                    limit: 10000,
                },
            },
            {
                test: /\.(scss|sass|css)$/,
                use: ExtractTextPlugin.extract({
                    fallback: 'style-loader',
                    use: [
                      { loader: 'css-loader' },
                      {
                        loader: 'postcss-loader',
                        options: {
                          sourceMap: true,
                          plugins: () => [autoprefixer({ browsers: ['iOS >= 7', 'Android >= 4.1'] })],
                        },
                      },
                      {
                         loader: 'sass-loader',
                         query: {
                           sourceMap: true
                         }
                      }
                    ]
                })
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            title: 'my title',
            filename: __dirname + '/dist/index.html',
            template: __dirname + '/index.html',
            chunks: ['main']
        }),
        new ExtractTextPlugin('assets/css/[name].css')
    ],
    devServer: {
        contentBase: path.join(__dirname, 'dist'),
        compress: true,
        port: 9000
    },
    resolve: {
        extensions: ['.js', '.json'],
        alias: {
            '@': path.resolve(__dirname, 'src'),
            '@Actions': path.resolve(__dirname, 'src', 'Actions'),
            '@Components': path.resolve(__dirname, 'src', 'Components'),
            '@Api': path.resolve(__dirname, 'src', 'Api'),
            '@Reduces': path.resolve(__dirname, 'src', 'Reduces'),
            '@Sagas': path.resolve(__dirname, 'src', 'Sagas'),
        }
    },
    devtool: 'source-map'
}
```

配置.babelrc

```js
{
  "presets": ["react"],
  "plugins": [
      ["import", { "libraryName": "antd", "style": "css" }]
  ]
}
```

配置package.json，加入scripts

```js
{
  ...
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

---

github地址： [https://github.com/dragon8github/react-study](https://github.com/dragon8github/react-study)

package.json

```js
{
  "name": "react-study",
  "version": "1.0.0",
  "main": "index.js",
  "repository": "https://github.com/dragon8github/react-study.git",
  "author": "dragon8github <928532756@qq.com>",
  "license": "MIT",
  "dependencies": {
    "antd": "^2.13.6",
    "autoprefixer": "^7.1.6",
    "axios": "^0.17.0",
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-plugin-import": "^1.6.2",
    "babel-preset-react": "^6.24.1",
    "bundle-loader": "^0.5.5",
    "css-loader": "^0.28.7",
    "extract-text-webpack-plugin": "^3.0.1",
    "file-loader": "^1.1.5",
    "history": "^4.7.2",
    "html-webpack-plugin": "^2.30.1",
    "node-sass": "^4.5.3",
    "postcss-loader": "^2.0.8",
    "react": "^16.0.0",
    "react-dom": "^16.0.0",
    "react-redux": "^5.0.6",
    "react-router": "^4.2.0",
    "react-router-dom": "^4.2.2",
    "react-router-redux": "next",
    "redux": "^3.7.2",
    "redux-saga": "^0.16.0",
    "redux-thunk": "^2.2.0",
    "sass-loader": "^6.0.6",
    "style-loader": "^0.19.0",
    "webpack": "^3.8.1",
    "webpack-dev-server": "^2.9.3"
  },
  "scripts": {
    "build": "webpack",
    "dev": "webpack-dev-server"
  }
}
```



