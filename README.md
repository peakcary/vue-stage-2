# vue-stage-2
vue-stage-2(组件)


建立vue组件
在src目录下建立components文件夹，并在其中建立app.vue文件，这样我们项目的目录结构如下：
```
|--dist             //webpack打包后生成的文件夹
|   |--build.js
|--node_modules     //项目的依赖所在的文件夹
|--src              //文件入口
|   |--components   //组件存放文件夹
|       |--app.vue  //组件
|   |--main.js      //主js文件
|--index.html       //主html文件
|--package.json
|--webpack.config.js //webpack配置文件  
```     
首先在index.hmtl中写入代码：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue example</title>
</head>
<body>
    <app></app>
    <script src="dist/build.js"></script>
</body>
</html>
```
在编辑器中打开app.vue文件，写入如下代码：
```
<template>
<div class="message">{{msg}}</div>    
</template>
<script>
export default {
  data () {
    return {
      msg: 'Hello from vue-loader'
    }
  }
}
</script>
<style>
.message{
    color:red;
    font-size:36px;
    font-weight:blod;
}
</style>
```
在main.js中写入：
```
import Vue from 'vue'
import App from './components/app.vue'

new Vue({
  el: 'body',
  components:{App}
});
```
这样运行命令webpack就可以看到效果了
这里用到了ES6的模块儿---import，export

export命令

export命令用于规定模块的对外接口。一个模块就是一个独立文件。该文件内的所有变量外部都无法获取。如果希望外部能够读取模块内部的某个变量，就必须使用export关键字对外暴露出该变量。例如：
```
//export.js
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;
```
这样就可以对外输出三个变量。

import命令

使用export对外暴露了接口后，其他js文件通过import命令加载这个模块文件。上面暴露的三个变量在另一个js文件中引入如下：

//import.js
import {firstName,lastName,year} from './export';
function setName(element){
    element.textContent = firstName + ' ' + lastName;
}
webpack的hot-reload
前端自动刷新现在已经很常见了，即改变页面后，浏览器自动刷新，但是这个功能在我们做单页面应用时候会很不好用，所以，webpack支持hot-reload(热替换)，当我们修改模块时候不会页面不会刷新，会直接在页面中生效。

hot-reload的基础---webpack-dev-server
webpack-dev-server支持两种模式的自动刷新页面：

iframe模式（页面嵌入一个iframe并在其中呈现页面的变化）
inline模式（一个小型的webpack-dev-server客户端会作为入口文件打包，这个客户端会在后端代码改变的时候刷新页面）
iframe模式

使用iframe模式无需额外的配置，在终端输入命令

$ webpack-dev-server
在浏览器中输入 http://loacalhost:8080/webpack-dev-server/index.html

inline模式

在dos下输入命令

$ webpack-dev-server --inline --hot
启动服务器，在浏览器中打开 http://loacalhost:8080 就可以看到我们的页面，此时修改app.vue中的css，以及html中的文字，都可以看到在浏览器中立马呈现。
关于webpack-dev-server的详细说明，可以参考官方文档或者博客WEBPACK DEV SERVER。

这里有一个问题需要说明下
在很多文章中都说，修改app.vue文件中script标签中的msg文字，会在浏览器中立即呈现效果，但是事实上我在做demo的时候并没有出现这个效果，Google了很多，找到了答案，尤大说：“data是初始值，但热更新的时候会保留当前状态”，原问题及答案链接。

至此，关于webpack+vue的基本结束，虽然简单，但是由于在这个过程中也遇到一些坑，所以总结下，关于对vue的研究，这才只是个开始...

附：
我的webpack配置文件：
```
var path = require('path');
module.exports = {
  entry: './src/main.js',
  output: {
    path: './dist',
    publicPath:'dist/',
    filename: 'build.js'
  },
  //配置自动刷新,如果打开会使浏览器刷新而不是热替换
  /*devServer: {
    historyApiFallback: true,
    hot: false,
    inline: true,
    grogress: true
  },*/
  module: {
    loaders: [
      //转化ES6语法
      {
        test: /\.js$/,
        loader: 'babel',
        exclude: /node_modules/
      },
      //解析.vue文件
      {
        test:/\.vue$/,
        loader:'vue'
      },
      //图片转化，小于8K自动转化为base64的编码
      {
        test: /\.(png|jpg|gif)$/,
        loader:'url-loader?limit=8192'
      }
    ]
  },
  vue:{
    loaders:{
      js:'babel'
    }
  },
  resolve: {
        // require时省略的扩展名，如：require('app') 不需要app.js
        extensions: ['', '.js', '.vue'],
        // 别名，可以直接使用别名来代表设定的路径以及其他
        alias: {
            filter: path.join(__dirname, './src/filters'),
            components: path.join(__dirname, './src/components')
        }
    },
    babel: {
       presets: ['es2015','stage-0'],
       plugins: ['transform-runtime']
    }    
}
```
特别说明
如果要在.babelrc下配置babel，则需要在根目录下新建该文件，windows环境下，不能新建该txt文件然后改后缀，需要通过dos命令建立：

echo>.babelrc
通过该命令就可以建立babelde配置文件，用编辑器打开，修改里面的内容为：
```
{
  "presets": ["es2015", "stage-0"],
  "plugins": ["transform-runtime"]
}
```
package.json文件：
```
{
  "name": "webpackvue",
  "version": "1.0.0",
  "description": "",
  "main": "vue.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.9.1",
    "babel-loader": "^6.2.4",
    "babel-plugin-transform-runtime": "^6.9.0",
    "babel-preset-es2015": "^6.9.0",
    "babel-preset-stage-0": "^6.5.0",
    "babel-runtime": "^6.9.2",
    "css-loader": "^0.23.1",
    "file-loader": "^0.8.5",
    "style-loader": "^0.13.1",
    "url-loader": "^0.5.7",
    "vue":"^1.0.24",
    "vue-router":"^0.7.13",
    "vue-hot-reload-api": "^1.3.2",
    "vue-html-loader": "^1.2.2",
    "vue-loader": "^8.5.2",
    "vue-style-loader": "^1.0.0",
    "webpack": "^1.13.1",
    "webpack-dev-server": "^1.14.1",
    "webpack-merge": "^0.13.0"
  }
}
```
