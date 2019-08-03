### gulp
gulp: 用于自动化构建的工具，有丰富的插件，以任务流的形式构建你的项目
* 易于使用：通过代码优于配置的策略，Gulp 让简单的任务简单，复杂的任务可管理。
* 构建快速：利用 Node.js 流的威力，你可以快速构建项目并减少频繁的 IO 操作。
* 插件高质：Gulp 严格的插件指南确保插件如你期望的那样简洁高质得工作。
* 易于学习：通过最少的 API，掌握 Gulp 毫不费力，构建工作尽在掌握：如同一系列流管道。

##### 安装：
```js
npm i -g gulp  
npm install --save-dev gulp
```
##### api:
* gulp.src(globs[, options])：入口文件
* gulp.dest(path[, options])：传入pipe的出口，可用相对路径
* gulp.task(name[, deps], fn)：定义一个使用 Orchestrator 实现的任务（task）。
>deps
类型： Array
一个包含任务列表的数组，这些任务会在你当前任务运行之前完成。

```js
gulp.task('mytask', ['array', 'of', 'task', 'names'], function() {
  // 做一些事
});
```
* gulp.watch(glob[, opts], tasks)：一个 glob 字符串，或者一个包含多个 glob 字符串的数组，用来指定具体监控哪些文件的变动。
* gulp.pipe()：用来将源文件传递到插件中
* gulp.series：合并task
* gulp.parallel：合并task

##### gulp4.0
```js
gulp.series(TASK,TASK...)
gulp.parallel(TASK,TASK...)
```

##### gulp插件列表：https://gulpjs.com/plugins/
##### gulp官网：https://gulpjs.com/