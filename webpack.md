### webpack
webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

四个核心概念：
*  入口(entry)
*  输出(output)
*  loader
*  插件(plugins)

##### 入口(entry)：
入口起点(entry point)指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。
单个入口（简写）语法:
```js
}
	entry: string|Array<string>
}
```
多入口对象语法:
```js
{
	entry: {[entryChunkName: string]: string|Array<string>}
}
```
##### 输出(output)：
output 属性告诉 webpack 在哪里输出它所创建的 bundles，以及如何命名这些文件，默认值为 ./dist。基本上，整个应用程序结构，都会被编译到你指定的输出路径的文件夹中。
用法：
```js
const config = {
  output: {
    filename: 'bundle.js',
    path: '/home/proj/public/assets'
  }
};

module.exports = config;
```
多个入口起点:
```js
{
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
}
```
output的常用属性：filename、path
其他可参照文档：https://www.webpackjs.com/configuration/output/
##### loader：
loader 让 webpack 能够去处理那些非 JavaScript 文件 **（webpack 自身只理解 JavaScript）**。loader 可以将所有类型的文件转换为 webpack 能够处理的有效模块，然后你就可以利用 webpack 的打包能力，对它们进行处理。

在更高层面，在 webpack 的配置中 loader 有两个目标：

* test 属性，用于标识出应该被对应的 loader 进行转换的某个或某些文件。
* use 属性，表示进行转换时，应该使用哪个 loader。

例子：
```js
const path = require('path');

const config = {
  output: {
    filename: 'my-first-webpack.bundle.js'
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  }
};

module.exports = config;
```
 ** 以上配置中，对一个单独的 module 对象定义了 rules 属性，里面包含两个必须属性：test 和 use。**
loader列表：https://www.webpackjs.com/loaders/
常用的loader：
* babel-loader
* css-loader
* style-loader
* url-loader
* file-loader
* sass-loader
* less-loader
* html-loader

##### 插件(plugins)：
loader 被用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。插件接口功能极其强大，可以用来处理各种各样的任务。
想要使用一个插件，你只需要 require() 它，然后把它添加到 plugins 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 new 操作符来创建它的一个实例。
```js
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
const webpack = require('webpack'); // 用于访问内置插件

const config = {
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

module.exports = config;
```
插件列表：https://www.webpackjs.com/plugins/
常用的是：模块热替换插件，其他可根据项目按需接入

##### 以上只是官网的一些概要，想深入了解webpack可查看官网文档：https://www.webpackjs.com/ ，建议自己编写一个loader和plugin，能更好的去理解webpack！