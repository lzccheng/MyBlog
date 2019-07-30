##### babel
babel主要是用于es6/7/8转为es5，Babel 是一个 JavaScript 编译器，中文文档：https://www.babeljs.cn/docs/index.html
Babel 是一个工具链，主要用于将 ECMAScript 2015+ 版本的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。下面列出的是 Babel 能为你做的事情：

语法转换
通过 Polyfill 方式在目标环境中添加缺失的特性 (通过 @babel/polyfill 模块)
源码转换 (codemods)

（从版本 7 开始）都是以 @babel 作为冠名的。这种模块化的设计能够让每种工具都针对特定使用情况进行设计。 

1、@babel/core  //核心库
```js
npm install --save-dev @babel/core
```
2、@babel/cli  //@babel/cli 是一个能够从终端（命令行）使用的工具
```js
npm install --save-dev @babel/cli

./node_modules/.bin/babel src --out-dir lib  //把src下的js转换并输出到lib目录下
```

3、插件和预设（preset）：代码转换功能以插件的形式出现，插件是小型的 JavaScript 程序，用于指导 Babel 如何对代码进行转换
* @babel/plugin-transform-arrow-functions  //箭头函数转换
* @babel/preset-env  //环境预设
```js
//babel.config.js
const presets = [
  [
    "@babel/env",
    {
      targets: {
        edge: "17",
        firefox: "60",
        chrome: "67",
        safari: "11.1",
      },
    },
  ],
];

module.exports = { presets };
//现在，名为 env 的 preset 只会为目标浏览器中没有的功能加载转换插件。
```
* @babel/polyfill   //主要模块之一，模块包括 core-js 和一个自定义的 regenerator runtime 模块用于模拟完整的 ES2015+ 环境。
这意味着你可以使用诸如 Promise 和 WeakMap 之类的新的内置组件、 Array.from 或 Object.assign 之类的静态方法、 Array.prototype.includes 之类的实例方法以及生成器函数（generator functions）（前提是你使用了 regenerator 插件）。为了添加这些功能，polyfill 将添加到全局范围（global scope）和类似 String 这样的内置原型（native prototypes）中。
```js
npm install --save @babel/polyfill
```
env preset 提供了一个 "useBuiltIns" 参数，当此参数设置为 "usage" 时，就会加载上面所提到的最后一个优化措施，也就是只包含你所需要的 polyfill。
```js
//babel.config.js
const presets = [
  [
    "@babel/env",
    {
      targets: {
        edge: "17",
        firefox: "60",
        chrome: "67",
        safari: "11.1",
      },
      useBuiltIns: "usage",  //env preset 提供了一个 "useBuiltIns" 参数，当此参数设置为 "usage" 时，就会加载上面所提到的最后一个优化措施，也就是只包含你所需要的 polyfill。
    },
  ],
];

module.exports = { presets };
```
Babel 将检查你的所有代码，以便查找目标环境中缺失的功能，然后只把必须的 polyfill 包含进来。
#### 总结:
使用 @babel/cli 从终端运行 Babel，利用 @babel/polyfill 来模拟所有新的 JavaScript 功能，而 env preset 只对所使用的并且目标浏览器中缺失的功能进行代码转换和加载 polyfill。

babel.config.js 文件可以满足你的的需求！
文档：https://www.babeljs.cn/docs/config-files#project-wide-configuration
在项目的根目录（package.json 文件所在目录）下创建一个名为 babel.config.js 的文件，并输入如下内容。

```js
module.exports = function (api) {
  api.cache(true);

  const presets = [ ... ];
  const plugins = [ ... ];

  return {
    presets,
    plugins
  };
}
```
.babelrc 文件适合你！
文档：https://www.babeljs.cn/docs/config-files#file-relative-configuration
在你的项目中创建名为 .babelrc 的文件，并输入以下内容。

```js
{
  "presets": [...],
  "plugins": [...]
}
```
package.json
还可以选择将 .babelrc 中的配置信息作为 babel 键（key）的值添加到 package.json 文件中，如下所示：
```js
{
  "name": "my-package",
  "version": "1.0.0",
  "babel": {
    "presets": [ ... ],
    "plugins": [ ... ],
  }
}
```
.babelrc.js
与 .babelrc 的配置相同，但你可以使用 JavaScript 编写。
```js
const presets = [ ... ];
const plugins = [ ... ];

module.exports = { presets, plugins };
```
你还可以调用 Node.js 的任何 API，例如基于进程环境进行动态配置：
```js
const presets = [ ... ];
const plugins = [ ... ];

if (process.env["ENV"] === "prod") {
  plugins.push(...);
}

module.exports = { presets, plugins };
```
@babel/cli文档：https://www.babeljs.cn/docs/babel-cli
@babel/core文档：https://www.babeljs.cn/docs/babel-core
